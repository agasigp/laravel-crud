<script setup>
import AppLayout from "@/Layouts/AppLayout.vue";
import Welcome from "@/Components/Welcome.vue";
import { TailwindPagination } from "laravel-vue-pagination";
import { ref } from "vue";
import { onMounted } from "vue";
import { router, Link, useForm } from "@inertiajs/vue3";
import Form from "@/Components/Form.vue";
import InputError from "@/Components/InputError.vue";
import InputLabel from "@/Components/InputLabel.vue";
import DefaultButton from "@/Components/Buttons/DefaultButton.vue";

const props = defineProps({
    product: {
        type: Object,
        default: () => ({}),
    },
});

const form = useForm({
    name: props.product.name,
    description: props.product.description,
    price: props.product.price,
    image: props.product.image,
    weight: props.product.weight,
    category: props.product.category,
});

const saveAction = () => {
    if (props.product.id) {
        form.put(route("products.update", props.product.id), {
            preserveScroll: true,
        });
    } else {
        form.post(route("products.store"), {
            preserveScroll: true,
        });
    }
};
</script>

<template>
    <AppLayout title="Master Produk">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Master Produk
            </h2>
        </template>

        <div class="py-6">
            <div class="max-w-screen-xl mx-auto sm:px-6 lg:px-8">
                <section class="bg-white dark:bg-gray-900">
                    <div class="py-8 px-4 mx-auto">
                        <h2
                            class="mb-4 text-xl font-bold text-gray-900 dark:text-white"
                        >
                            Tambah/Edit Produk
                        </h2>
                        <Form @submitted="saveAction">
                            <template #form>
                                <div class="grid gap-4 sm:grid-cols-2 sm:gap-6">
                                    <div class="sm:col-span-2">
                                        <label
                                            for="name"
                                            class="block mb-2 text-sm font-medium dark:text-white"
                                            :class="[
                                                $page.props.errors.name
                                                    ? 'text-red-600'
                                                    : 'text-gray-900',
                                            ]"
                                            >Nama Produk</label
                                        >
                                        <input
                                            type="text"
                                            v-model="form.name"
                                            id="name"
                                            :class="[
                                                $page.props.errors.name
                                                    ? 'bg-red-50 border border-red-500 text-red-900 placeholder-red-700 text-sm rounded-lg focus:ring-red-500 dark:bg-gray-700 focus:border-red-500 block w-full p-2.5 dark:text-red-500 dark:placeholder-red-500 dark:border-red-500'
                                                    : 'bg-gray-50 border border-gray-300 text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-primary-500 dark:focus:border-primary-500',
                                            ]"
                                            placeholder="Type product name"
                                        />
                                        <p
                                            class="mt-2 text-sm text-red-600 dark:text-red-500"
                                            v-if="$page.props.errors.name"
                                        >
                                            {{ $page.props.errors.name }}
                                        </p>
                                    </div>
                                    <div class="w-full">
                                        <label
                                            for="price"
                                            class="block mb-2 text-sm font-medium text-gray-900 dark:text-white"
                                            :class="[
                                                $page.props.errors.price
                                                    ? 'text-red-600'
                                                    : 'text-gray-900',
                                            ]"
                                            >Harga</label
                                        >
                                        <input
                                            type="number"
                                            v-model="form.price"
                                            id="price"
                                            :class="[
                                                $page.props.errors.price
                                                    ? 'bg-red-50 border border-red-500 text-red-900 placeholder-red-700 text-sm rounded-lg focus:ring-red-500 dark:bg-gray-700 focus:border-red-500 block w-full p-2.5 dark:text-red-500 dark:placeholder-red-500 dark:border-red-500'
                                                    : 'bg-gray-50 border border-gray-300 text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-primary-500 dark:focus:border-primary-500',
                                            ]"
                                            placeholder="Harga"
                                        />
                                        <p
                                            class="mt-2 text-sm text-red-600 dark:text-red-500"
                                            v-if="$page.props.errors.price"
                                        >
                                            {{ $page.props.errors.price }}
                                        </p>
                                    </div>
                                    <div>
                                        <label
                                            for="item-weight"
                                            class="block mb-2 text-sm font-medium text-gray-900 dark:text-white"
                                            :class="[
                                                $page.props.errors.weight
                                                    ? 'text-red-600'
                                                    : 'text-gray-900',
                                            ]"
                                            >Berat (kg)</label
                                        >
                                        <input
                                            type="number"
                                            v-model="form.weight"
                                            id="item-weight"
                                            :class="[
                                                $page.props.errors.weight
                                                    ? 'bg-red-50 border border-red-500 text-red-900 placeholder-red-700 text-sm rounded-lg focus:ring-red-500 dark:bg-gray-700 focus:border-red-500 block w-full p-2.5 dark:text-red-500 dark:placeholder-red-500 dark:border-red-500'
                                                    : 'bg-gray-50 border border-gray-300 text-sm rounded-lg focus:ring-primary-600 focus:border-primary-600 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-primary-500 dark:focus:border-primary-500',
                                            ]"
                                            placeholder="12"
                                        />
                                        <p
                                            class="mt-2 text-sm text-red-600 dark:text-red-500"
                                            v-if="$page.props.errors.weight"
                                        >
                                            {{ $page.props.errors.weight }}
                                        </p>
                                    </div>
                                    <div>
                                        <label
                                            for="category"
                                            class="block mb-2 text-sm font-medium text-gray-900 dark:text-white"
                                            >Kategori</label
                                        >
                                        <select
                                            v-model="form.category"
                                            id="category"
                                            class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-primary-500 focus:border-primary-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-primary-500 dark:focus:border-primary-500"
                                        >
                                            <option value="TV">
                                                TV/Monitors
                                            </option>
                                            <option value="PC">PC</option>
                                            <option value="GA">
                                                Gaming/Console
                                            </option>
                                            <option value="PH">Phones</option>
                                        </select>
                                    </div>

                                    <div class="sm:col-span-2">
                                        <label
                                            for="description"
                                            class="block mb-2 text-sm font-medium text-gray-900 dark:text-white"
                                            >Description</label
                                        >
                                        <textarea
                                            id="description"
                                            v-model="form.description"
                                            rows="8"
                                            class="block p-2.5 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-primary-500 focus:border-primary-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-primary-500 dark:focus:border-primary-500"
                                            placeholder="Your description here"
                                        ></textarea>
                                    </div>
                                </div>
                            </template>

                            <template #actions>
                                <DefaultButton class="mt-4" :size="'s'">
                                    Simpan
                                </DefaultButton>
                            </template>
                        </Form>
                    </div>
                </section>
            </div>
        </div>
    </AppLayout>
</template>
