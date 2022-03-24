<script>
import { Editor, EditorContent } from '@tiptap/vue-2'
import StarterKit from '@tiptap/starter-kit'
import Link from "@tiptap/extension-link"
import BulletList from '@tiptap/extension-bullet-list'
import ListItem from '@tiptap/extension-list-item'
import Image from "@tiptap/extension-image"

export default {
  components: {
    EditorContent,
  },

  data() {
    return {
      editor: new Editor({
        content: '<p>TipTap works, Yay!</p>',
        extensions: [
          StarterKit,
					Link,
					BulletList,
					ListItem,
					Image,
        ],
      }),

			editingLink: false,
      prevLink: null,
			outputFormat: null,
			editorOutput: '',
    }
  },

	methods: {
		makeH1() {
			this.editor.chain().toggleHeading({ level: 1 }).focus().run();
		},

		makeH2() {
			this.editor.chain().toggleHeading({ level: 2 }).focus().run();
		},

		makeBold() {
      this.editor.chain().toggleBold().focus().run();
    },

    makeItalic() {
      this.editor.chain().toggleItalic().focus().run()
    },

		toggleLinkDialog() {
      if (this.editingLink) {
        this.editingLink = false
        return
      }
      
      this.editingLink = true

      this.prevLink = this.editor.getAttributes("link").href
    },

    makeLink() {
      const url = this.prevLink

      // empty
      if (!url || url.trim() === "") {
        this.editor.chain().focus().extendMarkRange("link").unsetLink().run();
        this.editingLink = false

        return;
      }

      this.editor.chain().toggleLink({ href: url }).focus().run();

      this.editingLink = false
    },

    closeLinkDialog() {
      this.prevLink = null
      this.editingLink = false
    },

		makeList() {
      this.editor.chain().focus().toggleBulletList().run()
    },

		triggerImage() {
      document.getElementById("img-upload").click();
    },

    makeImage(e) {
      const file = e.target.files[0];
      const reader = new FileReader();

      reader.addEventListener(
        "load",
        () => {
          // convert image file to base64 string
          this.editor.chain().focus().setImage({ src: reader.result }).run();
        },
        false
      );

      if (file) {
        reader.readAsDataURL(file);
      }
    },

		produceOutput() {
      if (!this.outputFormat) {
        return
      }

      if (this.outputFormat === "json") {
        this.editorOutput = this.editor.getJSON()
      } else {
        this.editorOutput = this.editor.getHTML()
      }
    },
	},

  beforeDestroy() {
    this.editor.destroy()
  },
}
</script>

<template>
	<div>
		<div class=" md:w-3/4 lg:w-1/2 mx-4 sm:mx-auto border border-black rounded-md mb-4">
			<div class="flex flex-wrap justify-start items-center py-2 border-b border-black px-2 shadow-xs">
				<button 
					class="menu-button"
					:class="{'active-button': editor.isActive('heading', { level: 1 })}"
					@click="makeH1"
				>
					H1
				</button>

				<button
					class="menu-button"
					:class="{'active-button': editor.isActive('heading', { level: 2 })}"
					@click="makeH2"
				>
					H2
				</button>

				<button
					class="menu-button"
					:class="{'active-button': editor.isActive('bold')}"
					@click="makeBold"
				>
					B
				</button>

				<button
					class="menu-button"
					:class="{'active-button': editor.isActive('italic')}"
					@click="makeItalic"
				>
					I
				</button>

				<button
					class="menu-button"
					:class="{'active-button': editor.isActive('link')}"
					@click="toggleLinkDialog"
				>
					<svg style="width:24px;height:24px" viewBox="0 0 24 24">
						<path 
						fill="currentColor" 
						d="M3.9,12C3.9,10.29 5.29,8.9 7,8.9H11V7H7A5,5 0 0,0 2,12A5,5 0 0,0 7,17H11V15.1H7C5.29,15.1 3.9,13.71 3.9,12M8,13H16V11H8V13M17,7H13V8.9H17C18.71,8.9 20.1,10.29 20.1,12C20.1,13.71 18.71,15.1 17,15.1H13V17H17A5,5 0 0,0 22,12A5,5 0 0,0 17,7Z" />
					</svg>
				</button>

				<button
          class="menu-button"
          :class="{'active-button': editor.isActive('bulletList')}"
          @click="makeList">
            <svg style="width:24px;height:24px" viewBox="0 0 24 24">
              <path 
              fill="currentColor" 
              d="M7,5H21V7H7V5M7,13V11H21V13H7M4,4.5A1.5,1.5 0 0,1 5.5,6A1.5,1.5 0 0,1 4,7.5A1.5,1.5 0 0,1 2.5,6A1.5,1.5 0 0,1 4,4.5M4,10.5A1.5,1.5 0 0,1 5.5,12A1.5,1.5 0 0,1 4,13.5A1.5,1.5 0 0,1 2.5,12A1.5,1.5 0 0,1 4,10.5M7,19V17H21V19H7M4,16.5A1.5,1.5 0 0,1 5.5,18A1.5,1.5 0 0,1 4,19.5A1.5,1.5 0 0,1 2.5,18A1.5,1.5 0 0,1 4,16.5Z" />
            </svg>
        </button>

				<button
					class="menu-button"
					:class="{'active-button': editor.isActive('image')}"
					@click="triggerImage"
				>
					<svg style="width:24px;height:24px" viewBox="0 0 24 24">
						<path 
						fill="currentColor" 
						d="M7 19L12 14L13.88 15.88C13.33 16.79 13 17.86 13 19H7M10 10.5C10 9.67 9.33 9 8.5 9S7 9.67 7 10.5 7.67 12 8.5 12 10 11.33 10 10.5M13.09 20H6V4H13V9H18V13.09C18.33 13.04 18.66 13 19 13C19.34 13 19.67 13.04 20 13.09V8L14 2H6C4.89 2 4 2.9 4 4V20C4 21.11 4.89 22 6 22H13.81C13.46 21.39 13.21 20.72 13.09 20M18 15V18H15V20H18V23H20V20H23V18H20V15H18Z" />
					</svg>
				</button>

				<input type="file" id="img-upload" class="hidden" @change="makeImage" />

			</div>
			
			<div v-show="editingLink" class="flex py-4 justify-start items-center border-b border-black">
        <input 
          type="text"
          v-model="prevLink"
          class="w-3/5 inline-block mx-2 shadow-sm p-1 border border-black rounded"
          placeholder="Type URL here"
        >

        <button 
          class="mx-2 px-2 inline-block shadow-sm border border-black rounded" 
          title="save" 
          @click="makeLink"
        > 
          <svg style="width:24px;height:24px" viewBox="0 0 24 24">
            <path fill="currentColor" d="M21,7L9,19L3.5,13.5L4.91,12.09L9,16.17L19.59,5.59L21,7Z" />
          </svg> 
        </button>

        <button 
          class="mx-2 px-2 inline-block shadow-sm border border-black rounded" 
          title="close dialog" 
          @click="closeLinkDialog"
        > 
          <svg style="width:24px;height:24px" viewBox="0 0 24 24">
            <path fill="currentColor" d="M19,6.41L17.59,5L12,10.59L6.41,5L5,6.41L10.59,12L5,17.59L6.41,19L12,13.41L17.59,19L19,17.59L13.41,12L19,6.41Z" />
          </svg>
        </button>
      </div>

      <editor-content :editor="editor"/>
		</div>

		<div class="text-center">
      <button 
        type="button"
        class="border border-black rounded shadow-sm px-3 py-1 font-semibold mb-2"
        @click="produceOutput"
      >
        Show editor output
      </button>

      <div>
        <input type="radio" value="html" name="h-output" v-model="outputFormat" >
        <label for="h-output mr-2">As HTML</label>

        <input type="radio" value="json" name="j-output" v-model="outputFormat">
        <label for="h-output">As JSON</label>
      </div>

      <div>
        {{ editorOutput }}
      </div>
    </div>
	</div>
</template>