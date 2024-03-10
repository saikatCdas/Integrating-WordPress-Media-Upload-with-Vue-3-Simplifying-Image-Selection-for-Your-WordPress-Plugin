<template>
    <div>
        <MediaSelector :attachments="attachments" @onMediaSelected="handleMediaSelected" />
    </div>
</template>


<script setup>
import MediaSelector from './components/MediaSelector.vue';
import { ref } from 'vue';


const attachments = ref([
    {
        "id": 132,
        "title": "image",
        "filename": "image-20.png",
        "url": "http://wordpress.test/wp-content/uploads/2024/02/image-20.png",
        "description": "",
        "caption": "",
        "mime": "image/png",
        "filesizeInBytes": 708072,
        "height": 1208,
        "width": 800,
        "author": "1",
        "authorLink": "http://wordpress.test/wp-admin/profile.php",
        "authorName": "admin",
    }]);




const handleMediaSelected = (selectedMedia) => {
    attachments.value = selectedMedia;
};
</script>
