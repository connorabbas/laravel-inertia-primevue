<script setup>
import { ref, useTemplateRef } from 'vue';
import { useLazyDataTable } from '@/Composables/useLazyDataTable';
import { FilterMatchMode } from '@primevue/core/api';

const props = defineProps({
    auth: Object,
    users: Object,
});

const pageTitle = 'Users';
const breadcrumbs = [
    { label: 'Dashboard', route: route('admin.dashboard') },
    { label: pageTitle, route: route('admin.users.index') },
    { label: 'List' },
];

// User context menu
const userContextMenu = useTemplateRef('user-context-menu');
const userContextMenuItems = ref([]);
function toggleUserContextMenu(event, userData) {
    // Populate menu items based on row data
    userContextMenuItems.value = [
        {
            label: 'Manage User',
            icon: 'pi pi-pencil',
            command: () => {
                alert('User Data: ' + JSON.stringify(userData));
            },
        },
    ];
    // Show the menu
    userContextMenu.value.toggle(event);
}

// DataTable
const {
    processing,
    filters,
    sorting,
    firstDatasetIndex,
    filteredOrSorted,
    debounceInputFilter,
    paginate,
    filter,
    sort,
    hardReset,
} = useLazyDataTable(
    {
        name: { value: null, matchMode: FilterMatchMode.CONTAINS },
        email: { value: null, matchMode: FilterMatchMode.CONTAINS },
    },
    ['users'],
    props.users.per_page
);
</script>

<template>
    <InertiaHead :title="pageTitle" />

    <AuthenticatedAdminLayout
        :page-title="pageTitle"
        :breadcrumbs="breadcrumbs"
    >
        <template #titleEnd>
            <Button
                v-if="filteredOrSorted"
                severity="secondary"
                type="button"
                icon="pi pi-filter-slash"
                label="Clear Filters"
                outlined
                @click="hardReset"
            />
        </template>

        <Container :fluid="true">
            <div>
                <Card pt:body:class="p-3">
                    <template #content>
                        <Menu
                            ref="user-context-menu"
                            class="shadow-sm"
                            :model="userContextMenuItems"
                            popup
                        />
                        <DataTable
                            ref="dataTable"
                            v-model:filters="filters"
                            lazy
                            paginator
                            removableSort
                            resizableColumns
                            columnResizeMode="fit"
                            :loading="processing"
                            :value="users.data"
                            :totalRecords="users.total"
                            filterDisplay="row"
                            :sortField="sorting.field"
                            :sortOrder="sorting.order"
                            :rows="users.per_page"
                            :rowsPerPageOptions="[10, 20, 50, 100]"
                            :first="firstDatasetIndex"
                            paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                            currentPageReportTemplate="Showing {first} to {last} of {totalRecords} records"
                            @filter="filter"
                            @sort="sort"
                            @page="paginate"
                        >
                            <template #empty>
                                <div class="flex justify-center">
                                    <Message
                                        severity="warn"
                                        icon="pi pi-exclamation-circle"
                                        class="grow text-center flex justify-center"
                                    >
                                        No Users found.
                                    </Message>
                                </div>
                            </template>
                            <Column
                                field="name"
                                header="Name"
                                sortable
                                :showFilterMenu="false"
                            >
                                <template #filter="{ filterModel, filterCallback }">
                                    <InputText
                                        v-model="filterModel.value"
                                        type="text"
                                        placeholder="Search by name"
                                        fluid
                                        @input="debounceInputFilter(filterCallback)"
                                    />
                                </template>
                                <template #body="slotProps">
                                    {{ slotProps.data.name }}
                                </template>
                            </Column>
                            <Column
                                field="email"
                                header="Email"
                                sortable
                                :showFilterMenu="false"
                            >
                                <template #filter="{ filterModel, filterCallback }">
                                    <InputText
                                        v-model="filterModel.value"
                                        type="text"
                                        placeholder="Search by Email"
                                        fluid
                                        @input="debounceInputFilter(filterCallback)"
                                    />
                                </template>
                                <template #body="slotProps">
                                    {{ slotProps.data.email }}
                                </template>
                            </Column>
                            <Column header="Action">
                                <template #body="{ data }">
                                    <Button
                                        v-tooltip.top="'Show User Actions'"
                                        type="button"
                                        severity="secondary"
                                        text
                                        rounded
                                        icon="pi pi-ellipsis-v"
                                        @click="toggleUserContextMenu($event, data)"
                                    />
                                </template>
                            </Column>
                        </DataTable>
                    </template>
                </Card>
            </div>
        </Container>
    </AuthenticatedAdminLayout>
</template>
