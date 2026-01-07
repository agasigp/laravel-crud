<script setup>
import { computed, useSlots } from "vue";

defineEmits(["submitted"]);

const hasActions = computed(() => !!useSlots().actions);
</script>

<template>
    <form @submit.prevent="$emit('submitted')">
        <slot name="form" />

        <div v-if="hasActions">
            <slot name="actions" />
        </div>
    </form>
</template>
