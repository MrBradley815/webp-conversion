<script setup>
import { ref, onMounted } from 'vue';

const quality = ref('0.5')
const originalFileSize = ref('0')
const convertedFileSize = ref('0')

onMounted(() => {
  const inputEl = document.querySelector('input')
  
  inputEl.addEventListener('change', () => {
    const file = inputEl.files[0]
    console.log('File: ', file)
    originalFileSize.value = Math.round(file.size / 1000)
    const url = URL.createObjectURL(file)
    const img = new Image()
    console.log('Img: ', img)
    img.src = url
    img.onload = function() {
      const canvas = document.createElement('canvas')
      const ctx = canvas.getContext('2d')

      canvas.width = img.naturalWidth
      canvas.height = img.naturalHeight

      ctx.drawImage(img, 0, 0)

      canvas.toBlob((blob) => {
        const fr = new FileReader()

        fr.readAsDataURL(blob)

        fr.addEventListener('load', () => {
          const dataURL = fr.result
          const img2 = new Image()
          img2.src = dataURL
          convertedFileSize.value = Math.round(blob.size / 1000)

          const a = document.createElement('a')
          a.download = removeExtension(file.name)
          a.href = dataURL

          document.body.appendChild(a)

          a.click()
          a.remove()
        })
      }, 'image/webp', Number(quality.value))
    }
  })

  function removeExtension(filename) {
    return filename.substring(0, filename.lastIndexOf('.')) || filename
  }
})
</script>

<template>
  <main>
    <h1>Convert to WEBP</h1>
    <h3>Upload your jpg/png</h3>
    <label for="file-upload">C<span class="small">hoose</span> F<span class="small">ile</span></label>
    <input type="file" accept="image/*" id="file-upload" />
    <input type="range" min="0.1" max="0.9" step="0.1" v-model="quality" />
    <p class="quality">{{ quality }}</p>
    <p class="subtext">Quality</p>
    <hr>
    <p>Original Size: {{ originalFileSize }}KB</p>
    <p>Converted Size: {{ convertedFileSize }}KB</p>
  </main>
</template>