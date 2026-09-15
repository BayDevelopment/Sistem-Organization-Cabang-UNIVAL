<script setup lang="ts">
import { Head, Link } from '@inertiajs/vue3';
import {
    ArrowRight,
    ArrowUp,
    ArrowUpRight,
    CalendarDays,
    ChevronRight,
    Menu,
    Network,
    Newspaper,
    Search,
    X,
} from 'lucide-vue-next';
import { computed, onBeforeUnmount, onMounted, ref } from 'vue';

interface NewsItem {
    id: number;
    title: string;
    excerpt: string;
    category: string;
    date: string;
    image?: string | null;
}

const props = withDefaults(
    defineProps<{
        news?: NewsItem[];
    }>(),
    {
        news: () => [],
    },
);

/*
|--------------------------------------------------------------------------
| UI State
|--------------------------------------------------------------------------
*/
const mobileMenuOpen = ref(false);
const search = ref('');
const activeCategory = ref('Semua');
const pageReady = ref(false);
const showBackToTop = ref(false);

/*
|--------------------------------------------------------------------------
| Dummy News
|--------------------------------------------------------------------------
*/
const dummyNews: NewsItem[] = [
    {
        id: 1,
        title: 'Pengurus Pusat Gelar Rapat Koordinasi Nasional',
        excerpt:
            'Koordinasi nasional menjadi momentum memperkuat sinergi antarwilayah dan cabang dalam menjalankan program organisasi.',
        category: 'Organisasi',
        date: '12 September 2026',
        image: null,
    },
    {
        id: 2,
        title: 'Program Pengembangan Anggota Resmi Dimulai',
        excerpt:
            'Program pengembangan anggota dilaksanakan secara bertahap di seluruh wilayah untuk meningkatkan kapasitas dan kualitas anggota.',
        category: 'Kegiatan',
        date: '08 September 2026',
        image: null,
    },
    {
        id: 3,
        title: 'Digitalisasi Administrasi Organisasi',
        excerpt:
            'Transformasi digital membantu pengurus mengelola administrasi organisasi dengan lebih efisien dan terintegrasi.',
        category: 'Informasi',
        date: '03 September 2026',
        image: null,
    },
    {
        id: 4,
        title: 'Pengurus Wilayah Menyusun Program Kerja Tahunan',
        excerpt:
            'Penyusunan program kerja menjadi bagian penting dalam memastikan arah organisasi tetap terukur dan terarah.',
        category: 'Organisasi',
        date: '28 Agustus 2026',
        image: null,
    },
    {
        id: 5,
        title: 'Pelatihan Kepemimpinan Bagi Pengurus Cabang',
        excerpt:
            'Kegiatan pelatihan kepemimpinan dilaksanakan untuk memperkuat kemampuan pengurus dalam mengelola organisasi.',
        category: 'Kegiatan',
        date: '24 Agustus 2026',
        image: null,
    },
    {
        id: 6,
        title: 'Sistem Manajemen Organisasi Mulai Diterapkan',
        excerpt:
            'Implementasi sistem digital menjadi langkah awal menuju pengelolaan organisasi yang lebih modern dan terintegrasi.',
        category: 'Informasi',
        date: '20 Agustus 2026',
        image: null,
    },
];

/*
|--------------------------------------------------------------------------
| News Data
|--------------------------------------------------------------------------
*/
const displayedNews = computed(() =>
    props.news.length ? props.news : dummyNews,
);

const categories = computed(() => {
    const values = displayedNews.value.map((item) => item.category);

    return ['Semua', ...new Set(values)];
});

const filteredNews = computed(() => {
    const keyword = search.value.trim().toLowerCase();

    return displayedNews.value.filter((item) => {
        const matchesCategory =
            activeCategory.value === 'Semua' ||
            item.category === activeCategory.value;

        const matchesSearch =
            !keyword ||
            item.title.toLowerCase().includes(keyword) ||
            item.excerpt.toLowerCase().includes(keyword) ||
            item.category.toLowerCase().includes(keyword);

        return matchesCategory && matchesSearch;
    });
});

const featuredNews = computed(() => filteredNews.value[0] ?? null);

const otherNews = computed(() => filteredNews.value.slice(1));

/*
|--------------------------------------------------------------------------
| Scroll
|--------------------------------------------------------------------------
*/
function handleScroll() {
    showBackToTop.value = window.scrollY > 500;
}

function scrollToTop() {
    window.scrollTo({
        top: 0,
        behavior: 'smooth',
    });
}

/*
|--------------------------------------------------------------------------
| Lifecycle
|--------------------------------------------------------------------------
*/
onMounted(() => {
    window.addEventListener('scroll', handleScroll, {
        passive: true,
    });

    handleScroll();

    requestAnimationFrame(() => {
        pageReady.value = true;
    });
});

onBeforeUnmount(() => {
    window.removeEventListener('scroll', handleScroll);
});
</script>

<template>
    <Head title="Berita & Informasi" />

    <div
        class="min-h-screen overflow-x-hidden bg-white font-sans text-slate-950"
        :class="pageReady ? 'page-loaded' : 'page-loading'"
    >
        <!-- ========================================================= -->
        <!-- HEADER / NAVBAR - SAME AS WELCOME -->
        <!-- ========================================================= -->
        <header
            class="fixed inset-x-0 top-0 z-50 border-b border-white/10 bg-slate-950/90 backdrop-blur-xl"
        >
            <div
                class="mx-auto flex h-20 max-w-7xl items-center justify-between px-5 sm:px-6 lg:px-8"
            >
                <!-- Logo -->
                <Link
                    href="/"
                    class="group flex items-center gap-3"
                >
                    <div
                        class="flex h-10 w-10 items-center justify-center rounded-xl bg-white text-sm font-black text-slate-950 transition-transform duration-300 group-hover:rotate-3"
                    >
                        OM
                    </div>

                    <div class="hidden sm:block">
                        <div
                            class="text-[11px] font-bold tracking-[0.28em] text-white"
                        >
                            ORGANIZATION
                        </div>

                        <div
                            class="text-[9px] font-medium tracking-[0.22em] text-slate-400"
                        >
                            MANAGEMENT SYSTEM
                        </div>
                    </div>
                </Link>

                <!-- Desktop Navigation -->
                <nav class="hidden items-center gap-8 lg:flex">
                    <Link
                        href="/"
                        class="text-sm font-medium text-slate-300 transition hover:text-white"
                    >
                        Beranda
                    </Link>

                    <Link
                        href="/#profil"
                        class="text-sm font-medium text-slate-300 transition hover:text-white"
                    >
                        Profil
                    </Link>

                    <Link
                        href="/#struktur"
                        class="text-sm font-medium text-slate-300 transition hover:text-white"
                    >
                        Struktur
                    </Link>

                    <Link
                        href="/#ranting"
                        class="text-sm font-medium text-slate-300 transition hover:text-white"
                    >
                        Ranting & Unit
                    </Link>

                    <Link
                        href="/#kegiatan"
                        class="text-sm font-medium text-slate-300 transition hover:text-white"
                    >
                        Kegiatan
                    </Link>

                    <!-- Active News -->
                    <Link
                        href="/berita"
                        class="relative text-sm font-bold text-white"
                    >
                        Berita

                        <span
                            class="absolute -bottom-[30px] left-0 h-0.5 w-full rounded-full bg-white"
                        />
                    </Link>
                </nav>

                <!-- Login -->
                <div class="hidden items-center gap-3 lg:flex">
                    <Link
                        href="/login"
                        class="rounded-full border border-white/15 px-5 py-2.5 text-sm font-semibold text-white transition hover:border-white/30 hover:bg-white/10"
                    >
                        Masuk
                    </Link>
                </div>

                <!-- Mobile Button -->
                <button
                    type="button"
                    class="flex h-11 w-11 items-center justify-center rounded-xl border border-white/10 text-white transition hover:bg-white/5 lg:hidden"
                    aria-label="Toggle menu"
                    @click="mobileMenuOpen = !mobileMenuOpen"
                >
                    <X
                        v-if="mobileMenuOpen"
                        class="h-5 w-5"
                    />

                    <Menu
                        v-else
                        class="h-5 w-5"
                    />
                </button>
            </div>

            <!-- Mobile Navigation -->
            <Transition
                enter-active-class="transition duration-200 ease-out"
                enter-from-class="opacity-0 -translate-y-2"
                enter-to-class="opacity-100 translate-y-0"
                leave-active-class="transition duration-150 ease-in"
                leave-from-class="opacity-100 translate-y-0"
                leave-to-class="opacity-0 -translate-y-2"
            >
                <div
                    v-if="mobileMenuOpen"
                    class="border-t border-white/10 bg-slate-950 px-5 py-5 lg:hidden"
                >
                    <nav class="flex flex-col gap-2">
                        <Link
                            href="/"
                            class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 transition hover:bg-white/5 hover:text-white"
                            @click="mobileMenuOpen = false"
                        >
                            Beranda
                        </Link>

                        <Link
                            href="/#profil"
                            class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 transition hover:bg-white/5 hover:text-white"
                            @click="mobileMenuOpen = false"
                        >
                            Profil
                        </Link>

                        <Link
                            href="/#struktur"
                            class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 transition hover:bg-white/5 hover:text-white"
                            @click="mobileMenuOpen = false"
                        >
                            Struktur
                        </Link>

                        <Link
                            href="/#ranting"
                            class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 transition hover:bg-white/5 hover:text-white"
                            @click="mobileMenuOpen = false"
                        >
                            Ranting & Unit
                        </Link>

                        <Link
                            href="/#kegiatan"
                            class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 transition hover:bg-white/5 hover:text-white"
                            @click="mobileMenuOpen = false"
                        >
                            Kegiatan
                        </Link>

                        <Link
                            href="/berita"
                            class="rounded-xl bg-white/10 px-4 py-3 text-sm font-bold text-white"
                            @click="mobileMenuOpen = false"
                        >
                            Berita
                        </Link>

                        <Link
                            href="/login"
                            class="mt-3 rounded-xl bg-white px-4 py-3 text-center text-sm font-bold text-slate-950 transition hover:bg-slate-200"
                            @click="mobileMenuOpen = false"
                        >
                            Masuk ke Sistem
                        </Link>
                    </nav>
                </div>
            </Transition>
        </header>

        <!-- ========================================================= -->
        <!-- MAIN -->
        <!-- ========================================================= -->
        <main>
            <!-- Hero -->
            <section
                class="relative overflow-hidden bg-slate-950 pb-20 pt-40 text-white sm:pb-24"
            >
                <!-- Background blobs -->
                <div
                    class="pointer-events-none absolute -left-40 top-20 h-96 w-96 rounded-full bg-cyan-400/20 blur-3xl blob blob-one"
                />

                <div
                    class="pointer-events-none absolute right-[-12rem] top-10 h-[34rem] w-[34rem] rounded-full bg-blue-500/20 blur-3xl blob blob-two"
                />

                <div
                    class="pointer-events-none absolute bottom-[-14rem] left-1/3 h-[30rem] w-[30rem] rounded-full bg-violet-500/15 blur-3xl blob blob-three"
                />

                <!-- Grid -->
                <div
                    class="pointer-events-none absolute inset-0 opacity-[0.06]"
                    style="
                        background-image:
                            linear-gradient(rgba(255,255,255,.8) 1px, transparent 1px),
                            linear-gradient(90deg, rgba(255,255,255,.8) 1px, transparent 1px);
                        background-size: 44px 44px;
                    "
                />

                <div
                    class="relative mx-auto max-w-7xl px-5 sm:px-6 lg:px-8"
                >
                    <div
                        class="max-w-3xl transition-all duration-700 ease-out"
                        :class="
                            pageReady
                                ? 'translate-y-0 opacity-100'
                                : 'translate-y-8 opacity-0'
                        "
                    >
                        <div
                            class="mb-6 inline-flex items-center gap-2 rounded-full border border-white/10 bg-white/5 px-4 py-2 text-xs font-bold uppercase tracking-[0.16em] text-slate-300 backdrop-blur"
                        >
                            <span
                                class="h-2 w-2 rounded-full bg-emerald-400"
                            />

                            <Newspaper class="h-3.5 w-3.5" />

                            Berita & Informasi
                        </div>

                        <h1
                            class="text-4xl font-black leading-[1.02] tracking-[-0.04em] sm:text-5xl lg:text-6xl"
                        >
                            Berita &
                            <span class="text-cyan-300">
                                kegiatan.
                            </span>
                        </h1>

                        <p
                            class="mt-7 max-w-2xl text-base leading-8 text-slate-400 sm:text-lg"
                        >
                            Dapatkan informasi terbaru mengenai kegiatan,
                            program, perkembangan, dan aktivitas organisasi
                            dari berbagai tingkatan kepengurusan.
                        </p>
                    </div>
                </div>
            </section>

            <!-- ===================================================== -->
            <!-- NEWS CONTENT -->
            <!-- ===================================================== -->
            <section
                class="relative overflow-hidden bg-slate-50 py-16 sm:py-20"
            >
                <div
                    class="pointer-events-none absolute -right-48 top-0 h-[34rem] w-[34rem] rounded-full bg-blue-200/40 blur-3xl"
                />

                <div
                    class="pointer-events-none absolute -left-40 bottom-[-10rem] h-[30rem] w-[30rem] rounded-full bg-violet-200/30 blur-3xl"
                />

                <div
                    class="relative mx-auto max-w-7xl px-4 sm:px-6 lg:px-8"
                >
                    <!-- Search & Filter -->
                    <div
                        class="mb-10 flex flex-col gap-4 lg:flex-row lg:items-center lg:justify-between"
                    >
                        <div class="relative w-full lg:max-w-md">
                            <Search
                                class="pointer-events-none absolute left-4 top-1/2 h-4 w-4 -translate-y-1/2 text-slate-400"
                            />

                            <input
                                v-model="search"
                                type="search"
                                placeholder="Cari berita..."
                                class="h-12 w-full rounded-xl border border-slate-200 bg-white pl-11 pr-4 text-sm font-medium text-slate-900 outline-none transition placeholder:text-slate-400 focus:border-slate-400 focus:ring-4 focus:ring-slate-100"
                            />
                        </div>

                        <div
                            class="flex gap-2 overflow-x-auto pb-1 scrollbar-none"
                        >
                            <button
                                v-for="category in categories"
                                :key="category"
                                type="button"
                                class="shrink-0 rounded-xl px-4 py-2.5 text-sm font-bold transition duration-300"
                                :class="
                                    activeCategory === category
                                        ? 'bg-slate-950 text-white shadow-sm'
                                        : 'border border-slate-200 bg-white text-slate-500 hover:border-slate-300 hover:text-slate-950'
                                "
                                @click="activeCategory = category"
                            >
                                {{ category }}
                            </button>
                        </div>
                    </div>

                    <!-- Empty -->
                    <div
                        v-if="!filteredNews.length"
                        class="rounded-3xl border border-dashed border-slate-300 bg-white px-6 py-20 text-center shadow-sm"
                    >
                        <div
                            class="mx-auto flex h-14 w-14 items-center justify-center rounded-2xl bg-slate-100 text-slate-400"
                        >
                            <Search class="h-6 w-6" />
                        </div>

                        <h2
                            class="mt-5 text-lg font-extrabold text-slate-950"
                        >
                            Berita tidak ditemukan
                        </h2>

                        <p
                            class="mx-auto mt-2 max-w-md text-sm leading-6 text-slate-500"
                        >
                            Coba gunakan kata kunci lain atau pilih kategori
                            yang berbeda.
                        </p>
                    </div>

                    <template v-else>
                        <!-- Featured -->
                        <article
                            v-if="featuredNews"
                            class="group relative mb-10 overflow-hidden rounded-[2rem] border border-slate-200 bg-white shadow-sm transition duration-500 hover:-translate-y-1 hover:shadow-2xl"
                        >
                            <div
                                class="grid min-h-[430px] lg:grid-cols-2"
                            >
                                <!-- Image -->
                                <div
                                    class="relative min-h-[280px] overflow-hidden bg-slate-950 lg:min-h-full"
                                >
                                    <img
                                        v-if="featuredNews.image"
                                        :src="featuredNews.image"
                                        :alt="featuredNews.title"
                                        class="h-full w-full object-cover transition duration-700 group-hover:scale-105"
                                    />

                                    <div
                                        v-else
                                        class="absolute inset-0 flex items-center justify-center overflow-hidden bg-slate-950"
                                    >
                                        <div
                                            class="absolute -left-20 -top-20 h-72 w-72 rounded-full bg-cyan-400/20 blur-3xl"
                                        />

                                        <div
                                            class="absolute -bottom-20 -right-20 h-80 w-80 rounded-full bg-violet-500/20 blur-3xl"
                                        />

                                        <div
                                            class="absolute inset-0 opacity-[0.12]"
                                            style="
                                                background-image:
                                                    linear-gradient(rgba(255,255,255,.5) 1px, transparent 1px),
                                                    linear-gradient(90deg, rgba(255,255,255,.5) 1px, transparent 1px);
                                                background-size: 32px 32px;
                                            "
                                        />

                                        <Newspaper
                                            class="relative h-20 w-20 text-white/20"
                                        />
                                    </div>

                                    <div
                                        class="absolute left-5 top-5 rounded-full bg-white px-3 py-1.5 text-xs font-extrabold text-slate-950 shadow-sm"
                                    >
                                        {{ featuredNews.category }}
                                    </div>
                                </div>

                                <!-- Content -->
                                <div
                                    class="flex flex-col justify-center p-7 sm:p-10 lg:p-12"
                                >
                                    <div
                                        class="mb-5 flex flex-wrap items-center gap-3 text-xs font-bold uppercase tracking-[0.12em]"
                                    >
                                        <span class="text-blue-600">
                                            {{ featuredNews.category }}
                                        </span>

                                        <span
                                            class="h-1 w-1 rounded-full bg-slate-300"
                                        />

                                        <span
                                            class="inline-flex items-center gap-1.5 text-slate-400"
                                        >
                                            <CalendarDays
                                                class="h-3.5 w-3.5"
                                            />

                                            {{ featuredNews.date }}
                                        </span>
                                    </div>

                                    <h2
                                        class="text-2xl font-extrabold leading-tight tracking-[-0.025em] text-slate-950 sm:text-3xl lg:text-4xl"
                                    >
                                        {{ featuredNews.title }}
                                    </h2>

                                    <p
                                        class="mt-5 max-w-xl text-sm leading-7 text-slate-500 sm:text-base"
                                    >
                                        {{ featuredNews.excerpt }}
                                    </p>

                                    <div class="mt-8">
                                        <span
                                            class="inline-flex items-center gap-2 rounded-xl bg-slate-950 px-5 py-3 text-sm font-bold text-white transition duration-300 hover:-translate-y-0.5 hover:bg-slate-800"
                                        >
                                            Baca selengkapnya

                                            <ArrowRight
                                                class="h-4 w-4 transition-transform duration-300 group-hover:translate-x-1"
                                            />
                                        </span>
                                    </div>
                                </div>
                            </div>
                        </article>

                        <!-- Other News -->
                        <div
                            v-if="otherNews.length"
                            class="grid gap-6 md:grid-cols-2 lg:grid-cols-3"
                        >
                            <article
                                v-for="(item, index) in otherNews"
                                :key="item.id"
                                class="group flex flex-col overflow-hidden rounded-3xl border border-slate-200 bg-white shadow-sm transition-all duration-500 hover:-translate-y-1 hover:shadow-xl"
                                :style="{
                                    transitionDelay: `${index * 70}ms`,
                                }"
                            >
                                <!-- Image -->
                                <div
                                    class="relative aspect-[16/9] overflow-hidden bg-slate-950"
                                >
                                    <img
                                        v-if="item.image"
                                        :src="item.image"
                                        :alt="item.title"
                                        class="h-full w-full object-cover transition duration-700 group-hover:scale-105"
                                    />

                                    <div
                                        v-else
                                        class="absolute inset-0 flex items-center justify-center overflow-hidden"
                                    >
                                        <div
                                            class="absolute -left-10 -top-10 h-40 w-40 rounded-full bg-cyan-400/20 blur-3xl"
                                        />

                                        <div
                                            class="absolute -bottom-20 -right-10 h-52 w-52 rounded-full bg-violet-500/20 blur-3xl"
                                        />

                                        <Newspaper
                                            class="relative h-12 w-12 text-white/20"
                                        />

                                        <div
                                            class="absolute inset-0 opacity-[0.1]"
                                            style="
                                                background-image:
                                                    linear-gradient(rgba(255,255,255,.5) 1px, transparent 1px),
                                                    linear-gradient(90deg, rgba(255,255,255,.5) 1px, transparent 1px);
                                                background-size: 28px 28px;
                                            "
                                        />
                                    </div>

                                    <div
                                        class="absolute left-4 top-4 rounded-full bg-white px-3 py-1.5 text-[11px] font-extrabold text-slate-950 shadow-sm"
                                    >
                                        {{ item.category }}
                                    </div>
                                </div>

                                <!-- Card Content -->
                                <div
                                    class="flex flex-1 flex-col p-6"
                                >
                                    <div
                                        class="flex items-center gap-2 text-xs font-semibold text-slate-400"
                                    >
                                        <CalendarDays
                                            class="h-3.5 w-3.5"
                                        />

                                        {{ item.date }}
                                    </div>

                                    <h3
                                        class="mt-4 line-clamp-2 text-lg font-extrabold leading-snug tracking-tight text-slate-950 transition group-hover:text-blue-600"
                                    >
                                        {{ item.title }}
                                    </h3>

                                    <p
                                        class="mt-3 line-clamp-3 text-sm leading-6 text-slate-500"
                                    >
                                        {{ item.excerpt }}
                                    </p>

                                    <div class="mt-auto pt-6">
                                        <span
                                            class="inline-flex items-center gap-2 text-sm font-extrabold text-slate-950 transition group-hover:text-blue-600"
                                        >
                                            Baca berita

                                            <ChevronRight
                                                class="h-4 w-4 transition-transform group-hover:translate-x-1"
                                            />
                                        </span>
                                    </div>
                                </div>
                            </article>
                        </div>
                    </template>
                </div>
            </section>

            <!-- ===================================================== -->
            <!-- CTA -->
            <!-- ===================================================== -->
            <section
                class="relative overflow-hidden bg-white py-24 sm:py-32"
            >
                <div
                    class="pointer-events-none absolute left-[-12rem] top-[-8rem] h-[32rem] w-[32rem] rounded-full bg-cyan-100/60 blur-3xl"
                />

                <div
                    class="pointer-events-none absolute bottom-[-10rem] right-[-12rem] h-[34rem] w-[34rem] rounded-full bg-violet-100/60 blur-3xl"
                />

                <div
                    class="relative mx-auto max-w-5xl px-5 text-center sm:px-6 lg:px-8"
                >
                    <div
                        class="rounded-[2.5rem] border border-slate-200 bg-slate-950 px-7 py-14 text-white shadow-2xl sm:px-12 sm:py-16"
                    >
                        <Network
                            class="mx-auto h-9 w-9 text-cyan-300"
                        />

                        <h2
                            class="mx-auto mt-7 max-w-3xl text-4xl font-black tracking-tight sm:text-5xl"
                        >
                            Tetap terhubung dengan aktivitas organisasi.
                        </h2>

                        <p
                            class="mx-auto mt-5 max-w-2xl text-base leading-8 text-slate-400"
                        >
                            Jelajahi struktur organisasi, kegiatan, dan
                            informasi terbaru melalui Organization Management
                            System.
                        </p>

                        <div class="mt-9">
                            <Link
                                href="/"
                                class="inline-flex items-center gap-2 rounded-full bg-white px-7 py-3.5 text-sm font-bold text-slate-950 transition hover:bg-slate-200"
                            >
                                Kembali ke Beranda

                                <ArrowRight class="h-4 w-4" />
                            </Link>
                        </div>
                    </div>
                </div>
            </section>
        </main>

        <!-- ========================================================= -->
        <!-- FOOTER - SAME AS WELCOME -->
        <!-- ========================================================= -->
        <footer
            class="border-t border-slate-200 bg-slate-950 text-white"
        >
            <div
                class="mx-auto max-w-7xl px-5 py-14 sm:px-6 lg:px-8"
            >
                <div
                    class="grid gap-12 md:grid-cols-[1.4fr_.6fr_.6fr]"
                >
                    <!-- Brand -->
                    <div>
                        <div class="flex items-center gap-3">
                            <div
                                class="flex h-10 w-10 items-center justify-center rounded-xl bg-white text-sm font-black text-slate-950"
                            >
                                OM
                            </div>

                            <div>
                                <div
                                    class="text-[11px] font-bold tracking-[0.25em]"
                                >
                                    ORGANIZATION
                                </div>

                                <div
                                    class="text-[9px] tracking-[0.2em] text-slate-500"
                                >
                                    MANAGEMENT SYSTEM
                                </div>
                            </div>
                        </div>

                        <p
                            class="mt-6 max-w-md text-sm leading-7 text-slate-500"
                        >
                            Sistem manajemen organisasi multi-cabang yang
                            membantu mengelola struktur, kepengurusan,
                            anggota, kegiatan, dan informasi organisasi secara
                            terintegrasi.
                        </p>
                    </div>

                    <!-- Navigasi -->
                    <div>
                        <h3
                            class="text-xs font-bold uppercase tracking-[0.2em] text-slate-400"
                        >
                            Navigasi
                        </h3>

                        <div class="mt-5 space-y-3">
                            <Link
                                href="/"
                                class="block text-sm text-slate-500 transition hover:text-white"
                            >
                                Beranda
                            </Link>

                            <Link
                                href="/#profil"
                                class="block text-sm text-slate-500 transition hover:text-white"
                            >
                                Profil
                            </Link>

                            <Link
                                href="/#struktur"
                                class="block text-sm text-slate-500 transition hover:text-white"
                            >
                                Struktur
                            </Link>

                            <Link
                                href="/#ranting"
                                class="block text-sm text-slate-500 transition hover:text-white"
                            >
                                Ranting & Unit
                            </Link>

                            <Link
                                href="/#kegiatan"
                                class="block text-sm text-slate-500 transition hover:text-white"
                            >
                                Kegiatan
                            </Link>

                            <Link
                                href="/berita"
                                class="block text-sm font-semibold text-white"
                            >
                                Berita
                            </Link>
                        </div>
                    </div>

                    <!-- Quick Access -->
                    <div>
                        <h3
                            class="text-xs font-bold uppercase tracking-[0.2em] text-slate-400"
                        >
                            Akses
                        </h3>

                        <div class="mt-5 space-y-3">
                            <Link
                                href="/login"
                                class="inline-flex items-center gap-2 text-sm text-slate-500 transition hover:text-white"
                            >
                                Masuk ke Sistem

                                <ArrowUpRight
                                    class="h-3.5 w-3.5"
                                />
                            </Link>

                            <Link
                                href="/berita"
                                class="block text-sm text-slate-500 transition hover:text-white"
                            >
                                Semua Berita
                            </Link>
                        </div>
                    </div>
                </div>

                <!-- Copyright -->
                <div
                    class="mt-14 flex flex-col gap-4 border-t border-white/10 pt-7 text-xs text-slate-600 sm:flex-row sm:items-center sm:justify-between"
                >
                    <p>
                        © {{ new Date().getFullYear() }} Organization
                        Management System. All rights reserved.
                    </p>

                    <div class="flex items-center gap-2">
                        <span
                            class="h-2 w-2 rounded-full bg-emerald-400"
                        />

                        System Operational
                    </div>
                </div>
            </div>
        </footer>

        <!-- Back To Top -->
        <Transition
            enter-active-class="transition duration-300"
            enter-from-class="opacity-0 translate-y-3"
            enter-to-class="opacity-100 translate-y-0"
            leave-active-class="transition duration-200"
            leave-from-class="opacity-100 translate-y-0"
            leave-to-class="opacity-0 translate-y-3"
        >
            <button
                v-if="showBackToTop"
                type="button"
                class="fixed bottom-6 right-6 z-40 flex h-12 w-12 items-center justify-center rounded-full bg-slate-950 text-white shadow-xl transition hover:-translate-y-1 hover:bg-slate-800"
                aria-label="Kembali ke atas"
                @click="scrollToTop"
            >
                <ArrowUp class="h-5 w-5" />
            </button>
        </Transition>
    </div>
</template>

<style scoped>
html {
    scroll-behavior: smooth;
}

.page-loading,
.page-loaded {
    opacity: 1;
}

.page-loaded {
    transition: opacity 700ms ease;
}

.blob {
    animation: blobFloat 14s ease-in-out infinite;
}

.blob-one {
    animation-delay: 0s;
}

.blob-two {
    animation-delay: -4s;
}

.blob-three {
    animation-delay: -8s;
}

@keyframes blobFloat {
    0%,
    100% {
        transform: translate3d(0, 0, 0) scale(1);
    }

    33% {
        transform: translate3d(25px, -20px, 0) scale(1.04);
    }

    66% {
        transform: translate3d(-20px, 25px, 0) scale(0.97);
    }
}

@media (prefers-reduced-motion: reduce) {
    .page-loading,
    .page-loaded,
    .blob {
        opacity: 1;
        transform: none;
        animation: none;
        transition: none;
    }
}
</style>
