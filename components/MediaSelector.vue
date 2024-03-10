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
const listenForMediaChange = () => {
    mediaFrame.on('select', function () {
        const attachments = mediaFrame.state()
            .get('selection').toJSON()
        emit('onMediaSelected', attachments)
    })
}



let preSelectedIds = [];
const isNumeric = (value) => {
    return /^\d+$/.test(value);
}
// Set up selected items id
const setUpPreSelectedIds = () => {
    preSelectedIds = [];
    if (
        Array.isArray(props.attachments) &&
        props.attachments.length > 0) {
        Object.values(props.attachments)
            .forEach((attachment, index) => {
                if (isNumeric(attachment['id'])) {
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



// Override the default media modal with a custom class
wp.media.view.Modal = wp.media.view.Modal.extend({
    className: 'your-custom-class',
});

onMounted(() => {
    if (!window.wp || !window.wp.media) {
        return;
    }
    // check is media frame exist or not
    setUpPreSelectedIds();
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

    setPreselected();
    // listen media change

    // Listen for media change
    listenForMediaChange();
});

</script>