<template>
  <div id="app" class="editor-container">
    <div class="toolbar">
      <button @click="back">⏪ Назад</button>
      <button @click="forward">⏩ Вперед</button>
      <button @click="format('h1')">H1</button>
      <button @click="format('p')">Абзац</button>
      <button @click="insertImage">🖼️ Картинка</button>
      <button @click="copyHtml">📋 Скопировать HTML</button>
    </div>
    <div
      ref="editor"
      class="editor"
      contenteditable="true"
      @input="saveHistory"
    >
      <p>Таким образом консультация с широким активом представляет собой интересный эксперимент проверки позиций, занимаемых участниками в отношении поставленных задач. С другой стороны постоянное информационно-пропагандистское обеспечение нашей деятельности представляет собой интересный эксперимент проверки форм развития. Идейные соображения высшего порядка, а также укрепление и развитие структуры влечет за собой процесс внедрения и модернизации соответствующий условий активизации. Задача организации, в особенности же реализация намеченных плановых заданий играет важную роль в формировании дальнейших направлений развития. Повседневная практика показывает, что постоянное информационно-пропагандистское обеспечение нашей деятельности играет важную роль в формировании существенных финансовых и административных условий.</p>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';

export default {
  name: 'WysiwygEditor',
  setup() {
    const editor = ref(null);
    const history = ref([]);
    const currentHistoryIndex = ref(-1);

    const saveHistory = () => {
      const content = editor.value.innerHTML;
      if (
        currentHistoryIndex.value === history.value.length - 1 &&
        history.value[currentHistoryIndex.value] === content
      ) {
        return;
      }
      history.value = history.value.slice(0, currentHistoryIndex.value + 1);
      history.value.push(content);
      currentHistoryIndex.value++;
    };

    const back = () => {
      if (currentHistoryIndex.value > 0) {
        currentHistoryIndex.value--;
        editor.value.innerHTML = history.value[currentHistoryIndex.value];
      }
    };

    const forward = () => {
      if (currentHistoryIndex.value < history.value.length - 1) {
        currentHistoryIndex.value++;
        editor.value.innerHTML = history.value[currentHistoryIndex.value];
      }
    };

    const format = (tag) => {
      document.execCommand('insertHTML', false, `<${tag}>${window.getSelection()}</${tag}>`);
      saveHistory();
    };

    const insertImage = () => {
  const url = prompt('Введите URL изображения:');
  if (url) {
    document.execCommand('insertHTML', false, `<img src="${url}" alt="Image" style="max-width: 100%;">`);
    saveHistory();
  }
};

    const copyHtml = () => {
      const content = editor.value.innerHTML;
      navigator.clipboard.writeText(content).then(() => {
        alert('HTML скопирован в буфер обмена!');
      });
    };

    onMounted(() => {
      saveHistory();
    });

    return {
      editor,
      back,
      forward,
      format,
      insertImage,
      copyHtml,
      saveHistory,
    };
  },
};
</script>

<style>
.editor-container {
  max-width: 800px;
  margin: 20px auto;
  border: 1px solid #ddd;
  border-radius: 5px;
  padding: 10px;
}

.toolbar {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}

.toolbar button {
  padding: 5px 10px;
  border: none;
  background: #007bff;
  color: white;
  cursor: pointer;
  border-radius: 3px;
}

.toolbar button:hover {
  background: #0056b3;
}

.editor {
  min-height: 200px;
  border: 1px solid #ddd;
  padding: 10px 30px 10px 30px;
  border-radius: 2px;
  outline: none;
  text-align: left;
}
</style>