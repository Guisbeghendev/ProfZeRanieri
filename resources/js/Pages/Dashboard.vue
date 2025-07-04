<script setup>
import AppLayout from '@/Layouts/AppLayout.vue';
import {Head, Link} from '@inertiajs/vue3';

const props = defineProps({
    auth: Object,
    userGroupsWithLatestGalleries: Array,
});

const userHasRole = (roleName) => {
    if (!props.auth || !props.auth.user) {
        return false;
    }
    return props.auth.user.roles && props.auth.user.roles.some(role => role.name === roleName);
};

const getFirstImageUrl = (gallery) => {
    if (gallery.images && gallery.images.length > 0 && gallery.images[0].path_original) {
        return `/storage/${gallery.images[0].path_original}`;
    }
    return '/images/placeholder_gallery.jpg';
};
</script>

<template>
    <AppLayout>
        <Head title="Dashboard"/>

        <template #title>
            <h1>Meu painel</h1>
        </template>

        <div class="py-12 px-4 sm:px-6 lg:px-8 max-w-screen-xl mx-auto">
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">

                <div v-if="userHasRole('admin')"
                     class="p-6 bg-green-100 rounded-lg shadow hover:shadow-lg transition cursor-pointer">
                    <h3 class="text-green-700 font-semibold mb-2">Painel Administrativo</h3>
                    <p>Acesse as configurações e gestão do sistema.</p>
                    <Link href="/admin/dashboard"
                          class="mt-4 inline-flex items-center px-4 py-2 bg-green-600 border border-transparent rounded-md font-semibold text-xs text-white uppercase tracking-widest hover:bg-green-700 active:bg-green-900 focus:outline-none focus:border-green-900 focus:ring ring-green-300 disabled:opacity-25 transition ease-in-out duration-150">
                        Acessar Admin
                    </Link>
                </div>
                <div v-if="userHasRole('fotografo')"
                     class="p-6 bg-yellow-100 rounded-lg shadow hover:shadow-lg transition cursor-pointer">
                    <h3 class="text-yellow-700 font-semibold mb-2">Painel do Fotógrafo</h3>
                    <p>Gerencie suas galerias e acervo fotográfico.</p>
                    <Link href="/fotografo/dashboard"
                          class="mt-4 inline-flex items-center px-4 py-2 bg-yellow-600 border border-transparent rounded-md font-semibold text-xs text-white uppercase tracking-widest hover:bg-yellow-700 active:bg-yellow-900 focus:outline-none focus:border-yellow-900 focus:ring ring-yellow-300 disabled:opacity-25 transition ease-in-out duration-150">
                        Acessar Painel
                    </Link>
                </div>

                <div class="mt-8 p-6 bg-purple-100 rounded-lg shadow lg:col-span-3">
                    <h2 class="text-purple-700 font-bold text-xl mb-4">Últimas Galerias Publicadas nos Seus Grupos</h2>

                    <div v-if="userGroupsWithLatestGalleries.length === 0" class="text-gray-600">
                        <p>Você não possui acesso a grupos com galerias recentes ou nenhuma galeria foi publicada ainda
                            nos seus grupos.</p>
                    </div>

                    <div v-for="group in userGroupsWithLatestGalleries" :key="group.id" class="mb-6 last:mb-0">
                        <h3 class="text-purple-600 font-semibold text-lg mb-3">{{ group.name }}</h3>
                        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
                            <div v-for="gallery in group.galleries" :key="gallery.id"
                                 class="bg-white rounded-lg shadow overflow-hidden flex flex-col hover:shadow-md transition">
                                <Link :href="`/galleries/${gallery.id}`">
                                    <img :src="getFirstImageUrl(gallery)" :alt="gallery.title"
                                         class="w-full h-48 sm:h-56 md:h-64 lg:h-72 xl:h-80 2xl:h-96 object-cover">
                                    <div class="p-4">
                                        <h4 class="font-bold text-md text-gray-800 truncate">{{ gallery.title }}</h4>
                                        <p class="text-gray-600 text-sm mt-1">
                                            Data: {{ new Date(gallery.event_date).toLocaleDateString('pt-BR') }}
                                        </p>
                                    </div>
                                </Link>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </AppLayout>
</template>
