<script setup>
import { useTemplateRef } from 'vue';
import { useForm } from '@inertiajs/vue3';
import ApplicationLogo from '@/components/ApplicationLogo.vue';
import LinksMenu from '@/components/primevue/LinksMenu.vue';
import LinksMenuBar from '@/components/primevue/LinksMenuBar.vue';

const emit = defineEmits(['open-nav']);

// User menu
const logoutForm = useForm({});
const userMenu = useTemplateRef('user-menu');
const userMenuItems = [
    {
        label: 'Profile',
        route: route('admin.profile.edit'),
        icon: 'pi pi-fw pi-user',
    },
    {
        label: 'Log Out',
        icon: 'pi pi-fw pi-sign-out',
        command: () => {
            logoutForm.post(route('admin.logout'));
        },
    },
];
const toggleUserMenu = (event) => {
    userMenu.value.childRef.toggle(event);
};
</script>

<template>
    <nav class="dynamic-bg border-b dynamic-border">
        <Container fluid>
            <LinksMenuBar
                pt:root:class="px-0 py-3 border-0 rounded-none dynamic-bg"
                pt:button:class="hidden"
            >
                <template #start>
                    <div class="flex items-center">
                        <div class="shrink-0 flex items-center">
                            <Button
                                class="flex lg:hidden mr-4"
                                outlined
                                severity="secondary"
                                icon="pi pi-bars"
                                pt:icon:class="text-xl"
                                @click="emit('open-nav')"
                            />
                            <InertiaLink
                                :href="route('welcome')"
                                class="mr-5"
                            >
                                <ApplicationLogo
                                    class="block h-10 w-auto fill-current text-surface-900 dark:text-surface-0"
                                />
                            </InertiaLink>
                            <Tag value="Primary">ADMIN</Tag>
                        </div>
                    </div>
                </template>
                <template #end>
                    <div class="flex items-center space-x-3">
                        <!-- User Dropdown Menu -->
                        <div class="flex flex-col">
                            <Button
                                id="user-menu-btn"
                                severity="secondary"
                                :label="$page.props.auth.user.name"
                                class="hidden sm:flex"
                                icon="pi pi-angle-down"
                                iconPos="right"
                                text
                                @click="toggleUserMenu($event)"
                            />
                            <Button
                                class="flex sm:hidden"
                                icon="pi pi-user"
                                rounded
                                severity="secondary"
                                text
                                @click="toggleUserMenu($event)"
                            />
                            <div
                                id="user-menu-append"
                                class="relative"
                            ></div>
                            <LinksMenu
                                ref="user-menu"
                                appendTo="#user-menu-append"
                                :model="userMenuItems"
                                pt:root:class="left-auto! top-0! right-0"
                                popup
                            />
                        </div>
                    </div>
                </template>
            </LinksMenuBar>
        </Container>
    </nav>
</template>
