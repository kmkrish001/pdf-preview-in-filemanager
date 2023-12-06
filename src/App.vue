<template>
  <div>
    <div class="file-container">
      <ejs-filemanager
        id="overview_file"
        :ajaxSettings="ajaxSettings"
        :view="view"
        :fileOpen="fileOpen"
      >
      </ejs-filemanager>
    </div>

    <div class="pdf-container">
      <div class="title-container">
        <span class="pdf-title title"></span>
        <button class="close-button" :click="onClicked">close</button>
      </div>

      <ejs-pdfviewer id="pdfviewer" ref="pdfView" :serviceUrl="serviceUrl">
      </ejs-pdfviewer>
    </div>
  </div>
</template>
<style>
.file-overview .sample-container {
  margin: 10px 10px 10px 10px;
}
</style>
<script>
import {
  FileManagerComponent,
  NavigationPane,
  Toolbar,
  DetailsView,
} from '@syncfusion/ej2-vue-filemanager';
import {
  PdfViewerComponent,
  Toolbar,
  Magnification,
  Navigation,
  LinkAnnotation,
  BookmarkView,
  ThumbnailView,
  Print,
  TextSelection,
  TextSearch,
  Annotation,
  FormFields,
  FormDesigner,
} from '@syncfusion/ej2-vue-pdfviewer';

let hostUrl = 'http://localhost:62870/';
export default {
  components: {
    'ejs-filemanager': FileManagerComponent,
    'ejs-pdfviewer': PdfViewerComponent,
  },
  data: function () {
    return {
      ajaxSettings: {
        url: hostUrl + 'api/FileManager/FileOperations',
        getImageUrl: hostUrl + 'api/FileManager/GetImage',
        uploadUrl: hostUrl + 'api/FileManager/Upload',
        downloadUrl: hostUrl + 'api/FileManager/Download',
      },
      serviceUrl:
        'https://services.syncfusion.com/vue/production/api/pdfviewer',
    };
  },
  provide: {
    filemanager: [NavigationPane, DetailsView, Toolbar],
  },
  methods: {
    fileOpen: function (args) {
      var fileObj = this.$refs.fileObject;
      let fileName: string = args.fileDetails.name;
      let filePath: string =
        args.fileDetails.filterPath.replace(/\\/g, '/') + fileName;
      let fileType: string = args.fileDetails.type;
      if (fileType == '.pdf') {
        debugger;
        this.showPDFViewer(fileName);
        this.getFileStream(filePath, true);
      }
    },

    //Shows the PDF viewer
    showPDFViewer: function (name: string) {
      document.getElementsByClassName('file-container')[0].style.visibility =
        'hidden';
      document.getElementsByClassName('pdf-container')[0].style.visibility =
        'visible';
      document.getElementsByClassName('pdf-title')[0].innerHTML = name;
    },
    // sends HTTPPost Request to read the file as fileStream.
    getFileStream: function (filePath: string, isPDF: boolean) {
      let ajax: XMLHttpRequest = new XMLHttpRequest();
      ajax.open('POST', hostUrl + 'api/FileManager/GetDocument', true);
      ajax.setRequestHeader('content-type', 'application/json');

      ajax.onreadystatechange = () => {
        if (ajax.readyState === 4) {
          if (ajax.status === 200 || ajax.status === 304) {
            if (isPDF) {
              var pdfviewer = this.$refs.pdfView.ej2Instances;
              pdfviewer.load(ajax.responseText, null);
              //opens the file in pdf viewer
              //this.pdfView.load(ajax.responseText, null);
            }
          }
        }
      };
      ajax.send(
        JSON.stringify({
          FileName: filePath,
          Action: !isPDF ? 'ImportFile' : 'LoadPDF',
        })
      );
    },
    onClicked: function () {
      document.getElementsByClassName('file-container')[0].style.visibility =
        'visible';
      document.getElementsByClassName('pdf-container')[0].style.visibility =
        'hidden';
    },
  },
};
</script>
