<!-- <template>
  <textarea ref="textarea"></textarea>
</template>

<script setup>
import {onMounted, ref, watch, defineProps , computed} from 'vue';
// import {useNuxtApp} from '#app';

// const toast = useToast()
const props = defineProps({
  modelValue: String,
  aiGeneratedText: String,
  isServerUpdating: Boolean
});
const emit = defineEmits(['update:modelValue', 'update:selectedText', 'update:model', 'update:selectedTag'])
const textarea = ref(null);
// const {$jodit} = useNuxtApp();
import { Jodit } from 'jodit';
const editorInstance = ref(null);
let savedSelectionRange = null;


const model = computed({
  get(){
    return props.isServerUpdating
  },
  set(){
    emit('update:model', props.isServerUpdating)
  }
})

onMounted(() => {
  if (textarea.value) {
    function Dummy(editor) {
      this.editor = editor;
    }

    Dummy.prototype.aiGeneratedText = function () {
      const editor = this.editor;
      const selection = editor.selection;

      if (selection && !selection.isCollapsed()) {
        // Save the selection range if needed
        savedSelectionRange = selection.range;

        // Get the closest element of the selection
        let selectedElement = selection.current();

        // If the current selection is within a text node, get the parent node
        if (selectedElement && selectedElement.nodeType === Node.TEXT_NODE) {
          selectedElement = selectedElement.parentNode;
        }

        // Determine the tag name
        const tagName = selectedElement ? selectedElement.tagName.toLowerCase() : 'none';

        emit('update:selectedTag', tagName);
        emit('update:selectedText', selection.sel.toString());
      } else {
        // toast.add({
        //   title: 'Please select a text',
        //   timeout: 2000,
        //   color: 'yellow'
        // });
        alert('Please select a text');
      }
    };



    editorInstance.value = new Jodit(textarea.value, {
      disabled: model.value,
      toolbarAdaptive: false,
      buttons: [
        {
          name: 'InsertDummyImage',
          text: 'AI Insert',
          tooltip: 'AI Insert',
          exec: (editor) => {
            const dummy = new Dummy(editor);
            dummy.aiGeneratedText(100, 100, 'f00', '000');
          }
        },
        'source', '|', 'bold', 'strikethrough', 'underline', 'italic', '|',
        'ul', 'ol', '|', 'outdent', 'indent', '|',
        'font', 'fontsize', 'brush', 'paragraph', '|',
        'table', 'link', '|',
        'align', 'undo', 'redo', '|',
        'hr', 'eraser', '|', 'print', '|',
      ],
    });

    setContent(props.modelValue);

    editorInstance.value.events.on('change', () => {
      const content = editorInstance.value.getEditorValue();
      emit('update:modelValue', content);
    });


    watch(() => props.aiGeneratedText, (newText) => {
      if (newText && savedSelectionRange) {
        const editor = editorInstance.value;
        // Restore the saved selection range
        editor.selection.selectRange(savedSelectionRange);

        const range = editor.selection.range;

        // Delete the currently selected content, if any
        range.deleteContents();

        // Create a document fragment to insert the new text
        const fragment = document.createDocumentFragment();
        const textNode = document.createTextNode(newText);
        fragment.appendChild(textNode);

        // Insert the new text node into the range
        range.insertNode(fragment);

        // After inserting the text, the selection is collapsed at the end of the inserted text
        // If you want to modify the selection afterwards, you can do so here

        // Update the editor's content. This might need adjustment based on your editor's API.
        editor.setEditorValue(editor.value);

        // Clear the selection to prevent immediate re-selection issues
        editor.selection.clear();
      }
    });


    // watch(() => props.isServerUpdating, (newValue) => {
    //   if (editorInstance) {
    //     editorInstance.value.setReadOnly(newValue);
    //     editorInstance.value.editor.setAttribute('contenteditable', newValue); // Disable/enable content area
    //   }
    // })
  }

});

// Method to set the content of the editor
function setContent(value) {
  if (editorInstance.value) {
    editorInstance.value.value = value;
  }
}

// Watch for changes in modelValue prop and update the Jodit content
watch(() => props.modelValue, (newValue) => {
  setContent(newValue);
});


</script>


<style>
@keyframes pulseAnimation {
  0% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.02);
    opacity: 0.7;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

.jodit-toolbar-button_InsertDummyImage  .jodit-toolbar-button__text {
  color: #fff!important;

}
.jodit-toolbar-button_InsertDummyImage {
  background: rgb(135, 59, 255);
  animation: pulseAnimation 2s infinite;
}


</style> -->


