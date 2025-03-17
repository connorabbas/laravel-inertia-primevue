<script setup>
import { ref, useTemplateRef, onMounted } from 'vue';
import { usePage, useForm } from '@inertiajs/vue3';
import LinksBreadcrumb from '@/components/primevue/LinksBreadcrumb.vue';
import LinksMenu from '@/components/primevue/LinksMenu.vue';
import MobileSidebarNavDrawer from '@/components/layout/admin/MobileSidebarNavDrawer.vue';
import Footer from '@/components/layout/admin/Footer.vue';
import SideMenuItems from '@/components/layout/admin/SideMenuItems.vue';
import ApplicationLogo from '@/components/ApplicationLogo.vue';

defineProps({
    pageTitle: {
        type: String,
        required: false,
    },
    breadcrumbs: {
        type: Array,
        required: false,
        default: () => [],
    },
});

const page = usePage();

/* eslint-disable @typescript-eslint/no-unused-vars */
const tailwindArbitraryValues = [
    'w-[18rem]', // sidebar width
    'lg:pl-[18rem]',
    'top-[57px]', // 57px header height for aura & nora theme
    'lg:pt-[57px]',
    'top-[61px]', // 61px header height for lara theme
    'lg:pt-[61px]',
    'top-[64px]', // 64px header height for material theme
    'lg:pt-[64px]',
];
/* eslint-enable @typescript-eslint/no-unused-vars */

const logoutForm = useForm({});
const logout = () => {
    logoutForm.post(route('admin.logout'));
};
const userMenuItems = [
    {
        label: 'Settings',
        route: route('admin.profile.edit'),
        icon: 'pi pi-fw pi-cog',
    },
    {
        label: 'Log Out',
        icon: 'pi pi-fw pi-sign-out',
        command: () => logout(),
    },
];
const userMenu = useTemplateRef('user-menu');
const toggleUserMenu = (event) => {
    userMenu.value.childRef.toggle(event);
};
const mobileUserMenu = useTemplateRef('mobile-user-menu');
const toggleMobileUserMenu = (event) => {
    mobileUserMenu.value.childRef.toggle(event);
};

// Drawer menus
const navDrawerOpen = ref(false);

// Dynamic header height for arbitrary class styling
const headerHeight = ref('');
const siteHeader = useTemplateRef('site-header');
onMounted(() => {
    if (siteHeader.value) {
        headerHeight.value = `${siteHeader.value.offsetHeight}px`;
    }
});
</script>

<template>
    <div class="h-screen flex flex-col">
        <Teleport to="body">
            <Toast position="top-center" />
            <MobileSidebarNavDrawer v-model="navDrawerOpen">
                <SideMenuItems />
            </MobileSidebarNavDrawer>
        </Teleport>

        <div class="flex-1">
            <!-- Desktop Sidebar -->
            <aside :class="[
                'w-[18rem] inset-0 hidden lg:block fixed overflow-y-auto overflow-x-hidden dynamic-bg border-r dynamic-border',
                `top-[${headerHeight}]`,
            ]">
                <div class="flex flex-col justify-between w-full h-full p-6">
                    <div>
                        <InertiaLink
                            :href="route('welcome')"
                            class="mr-5"
                        >
                            <ApplicationLogo
                                class="block h-10 w-auto fill-current text-surface-900 dark:text-surface-0" />
                        </InertiaLink>
                        <SideMenuItems />
                    </div>
                    <div>
                        <Button
                            id="user-menu-btn"
                            severity="secondary"
                            :label="page.props.auth.user.name"
                            size="large"
                            icon="pi pi-sort"
                            iconPos="right"
                            pt:root:class="flex justify-between"
                            fluid
                            text
                            @click="toggleUserMenu($event)"
                        />
                        <LinksMenu
                            ref="user-menu"
                            :model="userMenuItems"
                            popup
                        />
                    </div>
                </div>
            </aside>

            <!-- Scrollable Content -->
            <div :class="[
                'flex flex-col h-full lg:pl-[18rem]',
                `lg:pt-[${headerHeight}]`,
            ]">

                <!-- Breadcrumbs Nav -->
                <nav
                    v-if="breadcrumbs.length"
                    class="dynamic-bg border-b dynamic-border"
                >
                    <Container fluid vertical>
                        <div class="flex gap-6 items-center">
                            <div>
                                <Button
                                    severity="secondary"
                                    icon="pi pi-bars"
                                    text
                                    @click="emit('open-nav')"
                                />
                            </div>
                            <LinksBreadcrumb :model="breadcrumbs" />
                        </div>
                    </Container>
                </nav>

                <!-- Page Content -->
                <main
                    id="page-content"
                    class="grow"
                >
                    <slot />
                </main>

                <footer>
                    <Footer />
                </footer>
            </div>
        </div>
    </div>
</template>
