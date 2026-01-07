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
import { QuillEditor } from "@vueup/vue-quill";
import "@vueup/vue-quill/dist/vue-quill.snow.css";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import { library } from "@fortawesome/fontawesome-svg-core";
import { faSquarePlus, faTrash } from "@fortawesome/free-solid-svg-icons";
import AutoComplete from "@/Components/AutoComplete.vue";
import _, { map } from "underscore";

library.add(faSquarePlus, faTrash);

const props = defineProps({
    grant: {
        type: Object,
        default: () => ({}),
    },
    lecturers: {
        type: Array,
    },
    members: {
        type: Array,
    },
    proposal: {
        type: Object,
        default: () => ({}),
    },
});

const form = useForm({
    grant_id: props.grant.id,
    title: props.proposal.title,
    abstract: props.proposal.abstract,
    proposal_file: props.proposal.proposal_file,
    members: props.members.map(member => ({ user_id: member.user_id, name: member.name })),
});

const reviewers = ref([]);

const addMember = () => {
    form.members.push({ user_id: null, name: '' });
};

const removeMember = (index) => {
    form.members.splice(index, 1);
};

const saveAction = () => {
    form.transform((data) => ({
        ...data,
    })).post(route("grants.submit", props.grant.id), {
        preserveScroll: true,
    });
};

const filterReviews = _.debounce((query, index) => {
    fetchReviews(query, index);
}, 500);

async function fetchReviews(query, index) {
    const queryparams = {
        q: query,
        type: "member",
        grant_id: props.grant.id,
    };

    fetch(route("ajax.users", queryparams))
        .then((response) => {
            if (!response.ok) {
                throw new Error("Network response was not ok.");
            }
            return response.json();
        })
        .then((data) => {
            reviewers.value[index] = data.data;
        })
        .catch((error) => {
            return error;
        });
}

const handleSelectedReviewer = (reviewer, index) => {
    form.members[index].user_id = reviewer.id;
    form.members[index].name = reviewer.name;
    reviewers.value[index] = [];
};
</script>

<template>
    <AppLayout :title="`Submit Proposal ${grant.name}`">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Hibah
            </h2>
        </template>

        <div class="py-6">
            <div class="max-w-screen-2xl mx-auto sm:px-6 lg:px-8">
                <div class="card bg-base-100 card-border shadow-sm">
                    <div class="card-body">
                        <div class="card-title">
                            Submit Proposal {{ grant.name }}
                        </div>
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
                                        v-model="form.title"
                                        :class="[
                                            $page.props.errors.title
                                                ? 'input-error'
                                                : '',
                                        ]"
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.title"
                                    >
                                        {{ $page.props.errors.title }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Abstrak
                                    </legend>
                                    <div class="w-1/2 mb-10">
                                        <QuillEditor
                                            v-model:content="form.abstract"
                                            contentType="html"
                                            theme="snow"
                                        />
                                    </div>

                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.abstract"
                                    >
                                        {{ $page.props.errors.abstract }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Anggota
                                    </legend>
                                    <div
                                        v-for="(member, index) in form.members"
                                        :key="index"
                                        class="flex items-center mb-2"
                                    >
                                        <AutoComplete
                                            :data="reviewers[index]"
                                            :fetchData="
                                                (query) =>
                                                    filterReviews(query, index)
                                            "
                                            :reviewerNumber="index"
                                            :searchQuery="member.name"
                                            @selectedData="
                                                (reviewer) =>
                                                    handleSelectedReviewer(
                                                        reviewer,
                                                        index
                                                    )
                                            "
                                        />
                                        <button
                                            v-if="index === 0"
                                            type="button"
                                            class="ml-2 btn btn-primary"
                                            @click="addMember"
                                        >
                                            <FontAwesomeIcon
                                                :icon="['fas', 'square-plus']"
                                            />
                                        </button>
                                        <button
                                            v-else
                                            type="button"
                                            class="ml-2 btn btn-error"
                                            @click="removeMember(index)"
                                        >
                                            <FontAwesomeIcon
                                                :icon="['fas', 'trash']"
                                            />
                                        </button>
                                    </div>
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.members"
                                    >
                                        {{ $page.props.errors.members }}
                                    </p>

                                    <legend
                                        class="fieldset-legend text-gray-700"
                                    >
                                        Proposal
                                    </legend>
                                    <input
                                        type="file"
                                        class="file-input"
                                        :class="[
                                            $page.props.errors.proposal_file
                                                ? 'file-input-error'
                                                : '',
                                        ]"
                                        @input="
                                            form.proposal_file =
                                                $event.target.files[0]
                                        "
                                    />
                                    <p
                                        class="fieldset-label text-red-500"
                                        v-if="$page.props.errors.proposal_file"
                                    >
                                        {{ $page.props.errors.proposal_file }}
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
