<script setup>
import AppLayout from "@/Layouts/AppLayout.vue";
import { TailwindPagination } from "laravel-vue-pagination";
import { ref, computed } from "vue";
import { router, Link, usePage } from "@inertiajs/vue3";
import DefaultButton from "@/Components/Buttons/DefaultButton.vue";

const props = defineProps({
    products: {
        type: Object,
        default: () => ({}),
    },
});

const queries = ref({
    name: "",
    page: 1,
});

const pages = usePage();
const roles = computed(() => pages.props.auth.roles);

const getResults = async (page = 1) => {
    queries.value.page = page;
    router.get(
        props.products.path,
        { page: page, name: queries.value.name },
        { preserveState: true }
    );
};

const truncatedUserGuide = (userGuide) => {
    return userGuide.length > 50
        ? userGuide.substring(0, 50) + "..."
        : userGuide;
};

const formatDate = (dateString) => {
    const date = new Date(dateString);
    return format(date, "dd MMMM yyyy", { locale: id });
};

const checkSubmissionPeriod = (startDate, endDate) => {
    const start = new Date(startDate);
    const end = new Date(endDate);
    end.setDate(end.getDate() - 1);
    end.setHours(23, 59, 59, 999);
    const today = new Date();

    return roles.value[0] === "lecturer" && today >= start && today < end;
};
</script>

<template>
    <AppLayout title="Master Produk">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Produk
            </h2>
        </template>

        <div class="py-6">
            <div class="max-w-screen-xl mx-auto sm:px-6 lg:px-8">
                <div class="card bg-base-100 shadow-sm">
                    <div class="card-body">
                        <div
                            role="alert"
                            :class="`alert alert-${$page.props.flash.status}`"
                            v-if="$page.props.flash.message"
                        >
                            <svg
                                v-if="$page.props.flash.status === 'success'"
                                xmlns="http://www.w3.org/2000/svg"
                                class="h-6 w-6 shrink-0 stroke-current"
                                fill="none"
                                viewBox="0 0 24 24"
                            >
                                <path
                                    stroke-linecap="round"
                                    stroke-linejoin="round"
                                    stroke-width="2"
                                    d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"
                                />
                            </svg>
                            <svg
                                v-else
                                xmlns="http://www.w3.org/2000/svg"
                                fill="none"
                                viewBox="0 0 24 24"
                                class="h-6 w-6 shrink-0 stroke-current"
                            >
                                <path
                                    stroke-linecap="round"
                                    stroke-linejoin="round"
                                    stroke-width="2"
                                    d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                                ></path>
                            </svg>
                            <span>{{ $page.props.flash.message }}</span>
                        </div>
                        <form @submit.prevent="getResults(page, name)">
                            <div
                                class="inline-flex rounded-md shadow-xs"
                                role="group"
                            ></div>
                            <div class="join">
                                <input
                                    type="text"
                                    v-model="queries.name"
                                    class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                                    placeholder="Cari Nama Produk..."
                                />
                                <DefaultButton class="ms-4" :size="'s'">
                                    Cari
                                </DefaultButton>
                                <Link
                                    :href="route('products.create')"
                                    as="button"
                                    class="ms-4 px-3 py-2 text-sm font-medium text-center text-white bg-blue-700 rounded-lg hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
                                >
                                    Tambah
                                </Link>
                            </div>
                        </form>

                        <div
                            class="mt-4 relative overflow-x-auto bg-neutral-primary-soft shadow-xs rounded-base border border-default"
                        >
                            <table
                                class="w-full text-sm text-left rtl:text-right text-body"
                            >
                                <thead
                                    class="bg-neutral-secondary-soft border-b border-default"
                                >
                                    <tr>
                                        <th
                                            scope="col"
                                            class="px-6 py-3 font-medium"
                                        >
                                            No.
                                        </th>
                                        <th
                                            scope="col"
                                            class="px-6 py-3 font-medium"
                                        >
                                            Product name
                                        </th>
                                        <th
                                            scope="col"
                                            class="px-6 py-3 font-medium"
                                        >
                                            Description
                                        </th>
                                        <th
                                            scope="col"
                                            class="px-6 py-3 font-medium"
                                        >
                                            Price
                                        </th>
                                        <th
                                            scope="col"
                                            class="px-6 py-3 font-medium"
                                        >
                                            Image
                                        </th>
                                        <th
                                            scope="col"
                                            class="px-6 py-3 font-medium"
                                        >
                                            Action
                                        </th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr v-if="products.data.length == 0">
                                        <td
                                            colspan="5"
                                            class="odd:bg-neutral-primary even:bg-neutral-secondary-soft border-b border-default text-center"
                                        >
                                            Tidak ada data
                                        </td>
                                    </tr>

                                    <tr
                                        v-else
                                        v-for="(row, index) in products.data"
                                        class="odd:bg-neutral-primary even:bg-neutral-secondary-soft border-b border-default"
                                    >
                                        <th
                                            scope="row"
                                            class="px-6 py-4 font-medium text-heading whitespace-nowrap"
                                        >
                                            {{
                                                products.current_page == 1
                                                    ? index + 1
                                                    : index +
                                                      1 +
                                                      (products.per_page *
                                                          products.current_page -
                                                          products.per_page)
                                            }}
                                        </th>
                                        <td class="px-6 py-4">
                                            {{ row.name }}
                                        </td>
                                        <td class="px-6 py-4">
                                            {{ row.description }}
                                        </td>
                                        <td class="px-6 py-4">
                                            {{ row.price }}
                                        </td>
                                        <td class="px-6 py-4">
                                            {{ row.image }}
                                        </td>
                                        <td class="px-6 py-4">
                                            <a
                                                href="#"
                                                class="font-medium text-fg-brand hover:underline"
                                                >Edit</a
                                            >
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>

                        <div className="my-4 d-flex justify-content-center">
                            <TailwindPagination
                                :data="products"
                                @pagination-change-page="getResults"
                            />
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <dialog id="my_modal_2" class="modal">
            <div class="modal-box">
                <h3 class="text-lg font-bold">Hello!</h3>
                <p class="py-4">Press ESC key or click outside to close</p>
            </div>
            <form method="dialog" class="modal-backdrop">
                <button>close</button>
            </form>
        </dialog>
    </AppLayout>
</template>
