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
import _, { map } from "underscore";

const props = defineProps({
    grantTypes: {
        type: Array,
    },
    grant: {
        type: Object,
        default: () => ({}),
    },
});

const form = useForm({
    name: props.grant.name,
    grant_type_id: props.grant.grant_type_id,
    start_date: props.grant.start_date,
    end_date: props.grant.end_date,
    submit_start_date: props.grant.submit_start_date,
    review_date: props.grant.review_date,
    announcement_date: props.grant.announcement_date,
    signing_contract_date: props.grant.signing_contract_date,
    progress_report_date: props.grant.progress_report_date,
    final_report_date: props.grant.final_report_date,
    presentation_date: props.grant.presentation_date,
    user_guide: props.grant.user_guide,
    detail_score_link: props.grant.detail_score_link,
    contract_file: props.grant.contract_file,
});

onMounted(() => {
    form.grant_type_id = props.grant.grant_type_id ??= props.grantTypes[0].id;
});

const truncatedUserGuide = (userGuide) => {
    return userGuide.length > 50
        ? userGuide.substring(0, 50) + "..."
        : userGuide;
};

const saveAction = () => {
    if (props.grant.id) {
        form.transform((data) => ({
            ...data,
            user_guide:
                typeof form.user_guide === "string" ? null : form.user_guide,
            contract_file:
                typeof form.contract_file === "string" ? null : form.contract_file,
            _method: "PUT",
        })).post(route("grants.update", props.grant.id), {
            preserveScroll: true,
        });
    } else {
        form.transform((data) => ({
            ...data,
        })).post(route("grants.store"), {
            preserveScroll: true,
        });
    }
};

const filterReviews = _.debounce((query, reviewerNumber) => {
    fetchReviews(query, reviewerNumber);
}, 500);

async function fetchReviews(query, reviewerNumber) {
    const queryparams = {
        q: query,
        type: "all",
        is_reviewer: "true",
    };

    fetch(route("ajax.users", queryparams))
        .then((response) => {
            if (!response.ok) {
                throw new Error("Network response was not ok.");
            }
            return response.json();
        })
        .then((data) => {
            if (reviewerNumber === 1) {
                reviewers1.value = data.data;
            } else {
                reviewers2.value = data.data;
            }
        })
        .catch((error) => {
            return error;
        });
}

const handleSelectedReviewer = (reviewer, type) => {
    if (type === 1) {
        form.reviewer1_user_id = reviewer.id;
        reviewer1.value = reviewer.name;
        reviewers1.value = [];
    } else {
        form.reviewer2_user_id = reviewer.id;
        reviewer2.value = reviewer.name;
        reviewers2.value = [];
    }
};
</script>

<template>
    <AppLayout title="Master Jenis Hibah">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Hibah
            </h2>
        </template>

        <div class="py-6">
            <div class="max-w-screen-2xl mx-auto sm:px-6 lg:px-8">
                <div class="card bg-base-100 card-border shadow-sm">
                    <div class="card-body">
                        <div class="card-title">Tambah/Edit Hibah</div>
                        <Form @submitted="saveAction">
                            <template #form>
                                <fieldset class="fieldset">
                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Judul
                                    </legend>
                                    <input
                                        type="text"
                                        class="input w-1/2"
                                        placeholder="Judul Hibah"
                                        v-model="form.name"
                                        :class="[
                                            $page.props.errors.name
                                                ? 'input-error'
                                                : '',
                                        ]"
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.name"
                                    >
                                        {{ $page.props.errors.name }}
                                    </p>
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.year"
                                    >
                                        {{ $page.props.errors.year }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Jenis
                                    </legend>
                                    <select
                                        class="select w-6/12"
                                        v-model="form.grant_type_id"
                                    >
                                        <option
                                            v-for="grantType in grantTypes"
                                            :value="grantType.id"
                                        >
                                            {{ grantType.name }}
                                        </option>
                                    </select>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Mulai Pengajuan Proposal
                                    </legend>
                                    <input
                                        type="date"
                                        class="input w-1/2"
                                        placeholder="Tanggal Mulai Pengajuan Proposal"
                                        v-model="form.submit_start_date"
                                        :class="[
                                            $page.props.errors.submit_start_date
                                                ? 'input-error'
                                                : '',
                                        ]"
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.submit_start_date"
                                    >
                                        {{ $page.props.errors.submit_start_date }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Review Proposal
                                    </legend>
                                    <input
                                        type="date"
                                        class="input w-1/2"
                                        placeholder="Tanggal Mulai Pengajuan Proposal"
                                        v-model="form.review_date"
                                        :class="[
                                            $page.props.errors.review_date
                                                ? 'input-error'
                                                : '',
                                        ]"
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.review_date"
                                    >
                                        {{ $page.props.errors.review_date }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Pengumuman Pemenang
                                    </legend>
                                    <input
                                        type="date"
                                        class="input w-1/2"
                                        placeholder="Tanggal Mulai"
                                        v-model="form.announcement_date"
                                        :class="[
                                            $page.props.errors.announcement_date
                                                ? 'input-error'
                                                : '',
                                        ]"
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.announcement_date"
                                    >
                                        {{ $page.props.errors.announcement_date }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Batas Akhir Tanda Tangan Kontrak
                                    </legend>
                                    <input
                                        type="date"
                                        class="input w-1/2"
                                        placeholder="Tanggal Mulai"
                                        v-model="form.signing_contract_date"
                                        :class="[
                                            $page.props.errors.signing_contract_date
                                                ? 'input-error'
                                                : '',
                                        ]"
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.signing_contract_date"
                                    >
                                        {{ $page.props.errors.signing_contract_date }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Batas Akhir Laporan Kemajuan
                                    </legend>
                                    <input
                                        type="date"
                                        class="input w-1/2"
                                        placeholder="Tanggal Mulai"
                                        v-model="form.progress_report_date"
                                        :class="[
                                            $page.props.errors.progress_report_date
                                                ? 'input-error'
                                                : '',
                                        ]"
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.progress_report_date"
                                    >
                                        {{ $page.props.errors.progress_report_date }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Batas Akhir Laporan Akhir
                                    </legend>
                                    <input
                                        type="date"
                                        class="input w-1/2"
                                        placeholder="Tanggal Mulai"
                                        v-model="form.final_report_date"
                                        :class="[
                                            $page.props.errors.final_report_date
                                                ? 'input-error'
                                                : '',
                                        ]"
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.final_report_date"
                                    >
                                        {{ $page.props.errors.final_report_date }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Presentasi Hasil Penelitian
                                    </legend>
                                    <input
                                        type="date"
                                        class="input w-1/2"
                                        placeholder="Tanggal Mulai"
                                        v-model="form.presentation_date"
                                        :class="[
                                            $page.props.errors.presentation_date
                                                ? 'input-error'
                                                : '',
                                        ]"
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.presentation_date"
                                    >
                                        {{ $page.props.errors.presentation_date }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Buku Panduan
                                    </legend>
                                    <input
                                        type="file"
                                        class="file-input"
                                        :class="[
                                            $page.props.errors.user_guide
                                                ? 'file-input-error'
                                                : '',
                                        ]"
                                        @input="
                                            form.user_guide =
                                                $event.target.files[0]
                                        "
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.user_guide"
                                    >
                                        {{ $page.props.errors.user_guide }}
                                    </p>
                                    <p>
                                        <a
                                            class="link link-primary"
                                            :href="form.user_guide"
                                            target="_blank"
                                            >File Buku Panduan</a
                                        >
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Kontrak Penelitian
                                    </legend>
                                    <input
                                        type="file"
                                        class="file-input"
                                        :class="[
                                            $page.props.errors.contract_file
                                                ? 'file-input-error'
                                                : '',
                                        ]"
                                        @input="
                                            form.contract_file =
                                                $event.target.files[0]
                                        "
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.contract_file"
                                    >
                                        {{ $page.props.errors.contract_file }}
                                    </p>
                                    <p>
                                        <a
                                            class="link link-primary"
                                            :href="form.contract_file"
                                            target="_blank"
                                            >File Kontrak Penelitian</a
                                        >
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Link Penilaian Gform
                                    </legend>
                                    <input
                                        type="text"
                                        class="input w-1/2"
                                        placeholder="Link Penilaian Gform"
                                        v-model="form.detail_score_link"
                                        :class="[
                                            $page.props.errors.detail_score_link
                                                ? 'input-error'
                                                : '',
                                        ]"
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.detail_score_link"
                                    >
                                        {{ $page.props.errors.detail_score_link }}
                                    </p>
                                </fieldset>
                            </template>

                            <template #actions>
                                <div class="card-actions mt-6">
                                    <Link
                                        :href="route('grants.index')"
                                        class="btn btn-secondary"
                                        as="button"
                                    >
                                        Kembali
                                    </Link>
                                    <button
                                        type="submit"
                                        class="btn btn-primary"
                                        :class="{
                                            'opacity-50': form.processing,
                                        }"
                                        :disabled="form.processing"
                                    >
                                        <span
                                            class="loading loading-spinner loading-md"
                                            :class="{
                                                hidden: !form.processing,
                                            }"
                                        ></span>
                                        Simpan
                                    </button>
                                </div>
                            </template>
                        </Form>
                    </div>
                </div>
            </div>
        </div>
    </AppLayout>
</template>
