<template>
  <div>
    <div id="pdf">
    </div>
    <button @click="RenderPDF()">Turn On PDFtron</button>
  </div>
</template>

<script>
import Webviewer from '@pdftron/webviewer';

export default {
  name: 'Webviewer',
  data: function(){
    return {
      coordinates: {
        x1: 114.47999999999999,
        x2: 177.39599999999996,
        x3: 177.39599999999996,
        x4: 114.47999999999999,
        y1: 319.80965000000003,
        y2: 319.80965000000003,
        y3: 307.12790000000007,
        y4: 307.12790000000007,
      },
      pageNumArray: [5,6,7],
    }
  },
  methods: {
    RenderPDF(){
      Webviewer({
        path: `${process.env.BASE_URL}webviewer`,
        initialDoc: `https://pdftron.s3.amazonaws.com/downloads/pl/demo-annotated.pdf`,
        fullAPI: false,
      }, document.getElementById("pdf")).then((instance)=>{

        //Styling UI
        instance.UI.setFitMode(instance.UI.FitMode.FitWidth);
        instance.UI.disableElements(['header', 'toolsHeader', 'contextMenuPopup', 'textUnderlineToolButton', 'textSquigglyToolButton', 'textStrikeoutToolButton', 'linkButton', 'annotationCommentButton', 'annotationStyleEditButton', 'annotationCropButton', 'annotationRedactButton', 'annotationGroupButton','annotationUngroupButton', 'calibrateButton', 'fileAttachmentDownload', 'annotationStylePopup', 'printModal']);

        const { Annotations, annotationManager, documentViewer } = instance.Core;

        //Source Tagging Logic
        annotationManager.addEventListener('annotationSelected', (annotations, action)=> {
          console.log("////////////////")
          console.log(annotations, {
            coordinates: annotations[0].Jf[0],
            pageNum: annotations[0].uD,
            content: annotations[0].Hi['trn-annot-preview'] || annotations[0].OS,
            action
          } );
          console.log('////////////////')
        });

        //Higlighting Logic
        documentViewer.addEventListener('documentLoaded', ()=>{
          instance.setCurrentPageNumber(this.pageNumArray[1]);

          const allHighlights = [];

          for(let i=0;i<this.pageNumArray.length;i++){
            let highlight = new Annotations.TextHighlightAnnotation();
            highlight.PageNumber = this.pageNumArray[i];
            highlight.Quads = [{ x1: this.coordinates.x1, y1: this.coordinates.y1, x2: this.coordinates.x2, y2: this.coordinates.y2, x3: this.coordinates.x3, y3: this.coordinates.y3, x4: this.coordinates.x4, y4: this.coordinates.y4 }];
            allHighlights.push(highlight);
          }      

          annotationManager.addAnnotation(allHighlights);
          annotationManager.drawAnnotationsFromList(allHighlights);

        });
      });
    }
  }
}
</script>


<style scoped>
#pdf{
  height: 90vh;
  width: 500px;
  margin-left: 370px
}
button{
  background-color: azure;
  border: 1px solid aliceblue;
  margin-left: 45%;
}
</style>
