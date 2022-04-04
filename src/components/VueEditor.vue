<template>
  <vue-editor v-model="content" ::customModules="customModulesForEditor" :editorOptions="editorSettings" />
</template>

<script>
import { VueEditor, Quill } from "vue2-editor";
import mention from 'quill-mention'
import 'quill-mention/dist/dist/quill.mention.css';
Quill.register('modules/mention ', mention )



async function suggestPeople(searchTerm) {
  const allPeople = [
    {
      id: 1,
      value: "Fredrik Sundqvist"
    },
    {
      id: 2,
      value: "Patrik Sjölin"
    }
  ];
  return allPeople.filter(person => person.value.includes(searchTerm));
}

export default {
  components: {
    VueEditor
  },
  data() {
    return {
      content: "<h1>Initial Content</h1>",
      customModulesForEditor: [
        { alias: "mention", module: mention }
      ],
      editorSettings: {
        modules: {
          mention: {
                allowedChars: /^[A-Za-z\sÅÄÖåäö]*$/,
                mentionDenotationChars: ["@"],
                source: async function(searchTerm, renderList) {
                    const people = await suggestPeople(searchTerm);
                    renderList(people);
                }
              }
        }
      }
    };
  }
};
</script>
