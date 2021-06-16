<template>
  <editor-content :editor="editor" />
  <button @click='gerarPdf'>Download PDF</button>
</template>

<script>
import { Editor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'
import { jsPDF } from "jspdf";

export default {
  components: {
    EditorContent,
  },

  props: {
    modelValue: {
      type: Object,
      default: '',
    },
  },

  data() {
    return {
      editor: null,
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
      var doc = new jsPDF('landscape', 'pt', 'a4');
      doc.addHTML(this.editor.getHTML(), function() {
      doc.save("teste.pdf");
  });
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