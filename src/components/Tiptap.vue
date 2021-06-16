<template>
  <div>
    <editor-content :editor="editor" />
    <button @click='gerarPdf'>Download PDF</button>
    <div>
    <div  v-html="html" ref='container'></div>
    </div>
  </div>
</template>

<script>
import { Editor, EditorContent } from '@tiptap/vue-2'
import StarterKit from '@tiptap/starter-kit'
import { jsPDF } from "jspdf";
import * as html2canvas from 'html2canvas'

export default {
  components: {
    EditorContent,
  },

  props: {
    modelValue: {
      type: Object,
      default: function(){ return {} },
    },
  },

  data() {
    return {
      editor: null,
      html: null
    }
  },

  watch: {
    modelValue(value) {
      const isSame = this.editor.getJSON() === value

      if (isSame) {
        return
      }

      this.editor.commands.setContent(this.modelValue, false)
    },
  },

  methods: {
    gerarPdf() {

      this.html = this.editor.getHTML()
      this.$nextTick(this.gerarPdfDoComponente)
    },
    gerarPdfDoComponente() {
      html2canvas(this.$refs.container).then(canvas => {
        var img = canvas.toDataURL("image/png",1.0);  
        console.log(canvas)
        var doc = new jsPDF({unit:'px', format:'a4'});
        doc.addImage(img, 'JPEG', 20, 20,1, 1);
        doc.save('NOME-DO-PDF.pdf')
      })
    }
  },

  mounted() {
    this.editor = new Editor({
      content: this.modelValue,
      extensions: [
        StarterKit,
      ],
      onUpdate: () => {
        this.$emit('update:modelValue', this.editor.getJSON())
      },
    })
  },

  beforeUnmount() {
    this.editor.destroy()
  },
}
</script>