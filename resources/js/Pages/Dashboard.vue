<template>
    <InertiaHead title="Dashboard" />
    <AuthenticatedLayout>
        <template #header>
            <h1 class="font-bold text-2xl leading-tight">Dashboard</h1>
        </template>
        <Container>
            <div class="py-4 md:py-8">
                <Card>
                    <template #content>
                        <p class="m-0">You are logged in!</p>
                    </template>
                </Card>
            </div>
        </Container>
    </AuthenticatedLayout>
</template>
