<script setup>
defineProps({
    spacedMobile: {
        type: Boolean,
        default: true,
        required: false,
    },
    fluid: {
        type: Boolean,
        default: false,
        required: false,
    },
});
</script>

<template>
    <div :class="[
        { 'max-w-(--breakpoint-2xl) mx-auto': !fluid },
        spacedMobile ? 'px-4' : 'px-0',
        'md:px-8',
    ]">
        <slot />
    </div>
</template>
