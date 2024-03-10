## Integrating WordPress Media Upload with Vue 3: Simplifying Image Selection for Your WordPress Plugin
Incorporating media upload functionality can significantly enhance user interactions when building WordPress plugins. In this guide, we'll explore how to seamlessly integrate media upload capabilities using Vue 3. Specifically, we'll create a custom media frame that allows users to select or upload images effortlessly.


### Enqueue the Media
In the PHP file of your plugin where you enqueue scripts, ensure to include the following lines to enable WordPress media functionality:

```
if (function_exists('wp_enqueue_media')) {
   wp_enqueue_media();
}
```

### Setup Vue Component 
Let's dive into creating a Vue.js component named MediaSelector.vue that will handle the media selection functionality

```html
<template>
   <button @click="openMediaFrame">{{ title }}</button>
</template>


<script setup>
import { defineProps, defineEmits, onMounted } from 'vue';


const props = defineProps({
   // Whether you want to select a single file or multiple files
   multiple: false,
   // Button Title
   title: {
       default: 'Add Media'
   },
   // Title of the Button when you select a media file
   action_title: {
       default: 'Use This Media'
   }
});


let mediaFrame = null;
const emit = defineEmits(['onMediaSelected']);


// Function to open the media frame
const openMediaFrame = () => {
   if (mediaFrame == null) {
       return;
   }
   mediaFrame.open();
};
</script>
```


### Initialize Media Frame
In the onMounted hook, we'll initialize the media frame and set up listeners for media selection.

```
<script setup>
// Existing code...


onMounted(() => {
   if (!window.wp || !window.wp.media) {
       return;
   }
   mediaFrame = window.wp.media({
       title: 'Select or Upload Media',
       button: {
           text: props.action_title
       },
       library: {
           type: 'image'
       },
       multiple: props.multiple ? 'add' : false,
   });


   // Listen for media change
   listenForMediaChange();
});
</script>
```




### Listen for Media Selection
We'll listen to the media selection event and emit the selected attachments to the parent component.

```
const listenForMediaChange = () => {
   mediaFrame.on('select', function () {
       const attachments = mediaFrame.state()
           .get('selection').toJSON()
       emit('onMediaSelected', attachments)
   })
}
```

Note: If you select a single item or multiple items, the attachments array will contain the chosen item's information. Like url, id, filesize author, etc.


### Usage
Now, let's utilize this component in your Vue application:

```
<template>
   <div>
       <MediaSelector
           :attachments="attachments"
           @onMediaSelected="handleMediaSelected" />
   </div>
</template>


<script setup>
import MediaSelector from '@/components/MediaSelector.vue';
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

```


### Additional Customizations
If you want to handle pre-selected media, you can utilize the provided functions setUpPreSelectedIds and setPreselected. Additionally, you can customize the media modal by overriding its default class.

```
<script setup>
// Existing code…


let preSelectedIds = [];
const isNumeric = (value) => {
   return /^\d+$/.test(value);
}
// Set up selected items id
const setUpPreSelectedIds = () => {
   preSelectedIds = [];
   if (
       Array.isArray(props.attachments) &&
       props.attachments.length > 0)
   {
       Object.values(props.attachments)
           .forEach((attachment, index) =>
           {
           if (isNumeric(attachment['id']))
           {
               preSelectedIds.push(attachment['id'])
           }
       })
   }
}
// Select those media file from your attachments id 
const setPreselected = () => {
   mediaFrame.on('open', function () {
       let selection = mediaFrame.state().get('selection');
       preSelectedIds.forEach(function (id) {
           let attachment = window.wp.media.attachment(id);
           if (attachment) {
               selection.add(attachment)
           }
       }); // would be probably a good idea to check if it is indeed a non-empty array
   });
}


onMounted(() => {
   // check is media frame exist or not
   setUpPreSelectedIds();
   // initial media frame
   setPreselected();
   // listen media change
})




// Override the default media modal with a custom class
wp.media.view.Modal = wp.media.view.Modal.extend({
   className: 'your-custom-class',
});
</script>
```


### Conclusion
In this guide, I tried to demonstrate how to seamlessly integrate WordPress media upload functionality with Vue 3. By creating a custom media selector component, users can effortlessly select or upload images, enhancing the usability of your WordPress plugin. With these steps, you'll be well on your way to creating a more engaging and user-friendly WordPress experience. Happy coding!

Feel free to adjust and expand upon this guide to suit your specific requirements and audience preferences.


