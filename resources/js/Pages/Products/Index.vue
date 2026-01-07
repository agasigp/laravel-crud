<script setup>
import AppLayout from "@/Layouts/AppLayout.vue";
import { TailwindPagination } from "laravel-vue-pagination";
import { ref, computed } from "vue";
import { router, Link, usePage } from "@inertiajs/vue3";
import DefaultButton from "@/Components/Buttons/DefaultButton.vue";
import RedButton from "@/Components/Buttons/RedButton.vue";

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

// Tambahkan fungsi formatRupiah
function formatRupiah(value) {
    const num = Number(value) || 0;
    return `Rp ${new Intl.NumberFormat("id-ID", {
        minimumFractionDigits: 2,
        maximumFractionDigits: 2,
    }).format(num)}`;
}

// Tambahkan computed untuk memformat kategori
const formattedProducts = computed(() => {
    const map = {
        TV: "Televisi",
        PH: "Handphone",
        GA: "Gaming Console",
        PC: "PC",
    };
    return (props.products.data || []).map((p) => ({
        ...p,
        categoryName: map[p.category] ?? p.category,
    }));
});

// Ganti modal Flowbite dengan konfirmasi JS
function openModal(id) {
    if (!id) return;
    const confirmed = window.confirm(
        "Apakah Anda yakin ingin menghapus produk ini?"
    );
    if (confirmed) {
        router.delete(route("products.destroy", id), {
            preserveState: true,
        });
    }
}

const getResults = async (page = 1) => {
    queries.value.page = page;
    router.get(
        props.products.path,
        { page: page, name: queries.value.name },
        { preserveState: true }
    );
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
                <div
                    class="w-full bg-neutral-primary-soft p-6 border border-default rounded-base shadow-xs bg-white dark:bg-gray-900"
                >
                    <div
                        class="flex items-start sm:items-center p-4 text-sm text-fg-success-strong rounded-base bg-success-soft"
                        role="alert"
                        v-if="$page.props.flash.status"
                    >
                        <svg
                            class="w-4 h-4 me-2 shrink-0 mt-0.5 sm:mt-0"
                            aria-hidden="true"
                            xmlns="http://www.w3.org/2000/svg"
                            width="24"
                            height="24"
                            fill="none"
                            viewBox="0 0 24 24"
                            v-if="$page.props.flash.status === 'success'"
                        >
                            <path
                                stroke="currentColor"
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                stroke-width="2"
                                d="M10 11h2v5m-2 0h4m-2.592-8.5h.01M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z"
                            />
                        </svg>
                        <p>
                            <span>{{ $page.props.flash.message }}</span>
                        </p>
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
                                        Produk
                                    </th>
                                    <th
                                        scope="col"
                                        class="px-6 py-3 font-medium"
                                    >
                                        Kategori
                                    </th>
                                    <th
                                        scope="col"
                                        class="px-6 py-3 font-medium"
                                    >
                                        Deskripsi
                                    </th>
                                    <th
                                        scope="col"
                                        class="px-6 py-3 font-medium"
                                    >
                                        Harga
                                    </th>
                                    <th
                                        scope="col"
                                        class="px-6 py-3 font-medium"
                                    >
                                        Aksi
                                    </th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-if="products.data.length == 0">
                                    <td
                                        colspan="6"
                                        class="odd:bg-neutral-primary even:bg-neutral-secondary-soft border-b border-default text-center"
                                    >
                                        Tidak ada data
                                    </td>
                                </tr>

                                <tr
                                    v-else
                                    v-for="(row, index) in formattedProducts"
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
                                        {{ row.categoryName }}
                                    </td>
                                    <td class="px-6 py-4">
                                        {{ row.description }}
                                    </td>
                                    <td class="px-6 py-4">
                                        {{ formatRupiah(row.price) }}
                                    </td>
                                    <td class="px-6 py-4">
                                        <RedButton @click="openModal(row.id)"
                                            >Hapus</RedButton
                                        >

                                        <Link
                                            :href="
                                                route('products.edit', row.id)
                                            "
                                            class="focus:outline-none text-white bg-green-700 hover:bg-green-800 focus:ring-4 focus:ring-green-300 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2 dark:bg-green-600 dark:hover:bg-green-700 dark:focus:ring-green-800"
                                        >
                                            Edit
                                        </Link>
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
    </AppLayout>
</template>
