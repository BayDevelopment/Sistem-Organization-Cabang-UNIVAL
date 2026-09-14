<script setup lang="ts">
import { Head, Link } from '@inertiajs/vue3';
import {
    ArrowLeft,
    ArrowRight,
    ArrowUpRight,
    CalendarDays,
    ChevronRight,
    Menu,
    Network,
    Newspaper,
    Search,
    X,
} from 'lucide-vue-next';
import { computed, ref } from 'vue';

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

const mobileMenuOpen = ref(false);
const search = ref('');
const activeCategory = ref('Semua');

const fallbackNews: NewsItem[] = [
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
| Data
|--------------------------------------------------------------------------
| Sementara menggunakan dummy apabila backend belum mengirim data.
| Ketika backend sudah siap, fallbackNews bisa dihapus.
*/
const displayedNews = computed(() =>
    props.news.length ? props.news : fallbackNews,
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
</script>

<template>
    <Head title="Berita & Informasi" />

    <div class="min-h-screen bg-white font-sans text-slate-950">
        <!-- Navbar -->
        <header
            class="sticky top-0 z-50 border-b border-slate-200/80 bg-white/90 backdrop-blur-xl"
        >
            <div
                class="mx-auto flex h-20 max-w-7xl items-center justify-between px-4 sm:px-6 lg:px-8"
            >
                <!-- Logo -->
                <Link href="/" class="group flex items-center gap-3">
                    <div
                        class="flex h-10 w-10 items-center justify-center rounded-xl bg-slate-950 text-white shadow-sm transition-transform duration-300 group-hover:-rotate-3"
                    >
                        <Network class="h-5 w-5" />
                    </div>

                    <div class="leading-none">
                        <div
                            class="text-sm font-extrabold tracking-tight text-slate-950"
                        >
                            ORGANISASI
                        </div>
                        <div
                            class="mt-1 text-[10px] font-semibold uppercase tracking-[0.18em] text-slate-400"
                        >
                            Management System
                        </div>
                    </div>
                </Link>

                <!-- Desktop Navigation -->
                <nav class="hidden items-center gap-8 md:flex">
                    <Link
                        href="/"
                        class="text-sm font-semibold text-slate-500 transition hover:text-slate-950"
                    >
                        Beranda
                    </Link>

                    <a
                        href="/#tentang"
                        class="text-sm font-semibold text-slate-500 transition hover:text-slate-950"
                    >
                        Tentang
                    </a>

                    <a
                        href="/#fitur"
                        class="text-sm font-semibold text-slate-500 transition hover:text-slate-950"
                    >
                        Fitur
                    </a>

                    <Link
                        href="/berita"
                        class="relative text-sm font-bold text-slate-950"
                    >
                        Berita
                        <span
                            class="absolute -bottom-[27px] left-0 h-0.5 w-full rounded-full bg-slate-950"
                        />
                    </Link>
                </nav>

                <!-- Desktop Login -->
                <div class="hidden md:block">
                    <Link
                        href="/login"
                        class="inline-flex items-center gap-2 rounded-xl bg-slate-950 px-5 py-2.5 text-sm font-bold text-white transition duration-300 hover:-translate-y-0.5 hover:bg-slate-800"
                    >
                        Login
                        <ArrowUpRight class="h-4 w-4" />
                    </Link>
                </div>

                <!-- Mobile Button -->
                <button
                    type="button"
                    class="flex h-10 w-10 items-center justify-center rounded-xl border border-slate-200 text-slate-700 md:hidden"
                    aria-label="Toggle menu"
                    @click="mobileMenuOpen = !mobileMenuOpen"
                >
                    <X v-if="mobileMenuOpen" class="h-5 w-5" />
                    <Menu v-else class="h-5 w-5" />
                </button>
            </div>

            <!-- Mobile Navigation -->
            <div
                v-if="mobileMenuOpen"
                class="border-t border-slate-200 bg-white md:hidden"
            >
                <nav class="mx-auto flex max-w-7xl flex-col px-4 py-4 sm:px-6">
                    <Link
                        href="/"
                        class="rounded-xl px-4 py-3 text-sm font-semibold text-slate-600 hover:bg-slate-50"
                        @click="mobileMenuOpen = false"
                    >
                        Beranda
                    </Link>

                    <a
                        href="/#tentang"
                        class="rounded-xl px-4 py-3 text-sm font-semibold text-slate-600 hover:bg-slate-50"
                        @click="mobileMenuOpen = false"
                    >
                        Tentang
                    </a>

                    <a
                        href="/#fitur"
                        class="rounded-xl px-4 py-3 text-sm font-semibold text-slate-600 hover:bg-slate-50"
                        @click="mobileMenuOpen = false"
                    >
                        Fitur
                    </a>

                    <Link
                        href="/berita"
                        class="rounded-xl bg-slate-50 px-4 py-3 text-sm font-bold text-slate-950"
                        @click="mobileMenuOpen = false"
                    >
                        Berita
                    </Link>

                    <Link
                        href="/login"
                        class="mt-2 inline-flex items-center justify-center gap-2 rounded-xl bg-slate-950 px-4 py-3 text-sm font-bold text-white"
                        @click="mobileMenuOpen = false"
                    >
                        Login
                        <ArrowUpRight class="h-4 w-4" />
                    </Link>
                </nav>
            </div>
        </header>

        <main>
            <!-- Hero -->
            <section class="relative overflow-hidden">
                <!-- Background decoration -->
                <div
                    class="pointer-events-none absolute -left-32 -top-40 h-96 w-96 rounded-full bg-blue-100/70 blur-3xl"
                />
                <div
                    class="pointer-events-none absolute -right-32 top-20 h-96 w-96 rounded-full bg-cyan-100/60 blur-3xl"
                />

                <div
                    class="relative mx-auto max-w-7xl px-4 pb-16 pt-16 sm:px-6 md:pb-20 md:pt-24 lg:px-8"
                >
                    <div class="max-w-3xl">
                        <div
                            class="mb-6 inline-flex items-center gap-2 rounded-full border border-slate-200 bg-white px-3 py-1.5 text-xs font-bold uppercase tracking-[0.14em] text-slate-500 shadow-sm"
                        >
                            <Newspaper class="h-3.5 w-3.5" />
                            Berita & Informasi
                        </div>

                        <h1
                            class="text-4xl font-extrabold leading-[1.05] tracking-[-0.04em] text-slate-950 sm:text-5xl lg:text-6xl"
                        >
                            Berita &
                            <span class="text-blue-600">kegiatan.</span>
                        </h1>

                        <p
                            class="mt-6 max-w-2xl text-base leading-7 text-slate-500 sm:text-lg"
                        >
                            Dapatkan informasi terbaru mengenai kegiatan,
                            program, perkembangan, dan aktivitas organisasi dari
                            berbagai tingkatan kepengurusan.
                        </p>
                    </div>
                </div>
            </section>

            <!-- Content -->
            <section class="border-t border-slate-100 bg-slate-50/70">
                <div
                    class="mx-auto max-w-7xl px-4 py-10 sm:px-6 sm:py-14 lg:px-8"
                >
                    <!-- Search & Filter -->
                    <div
                        class="mb-10 flex flex-col gap-4 lg:flex-row lg:items-center lg:justify-between"
                    >
                        <!-- Search -->
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

                        <!-- Categories -->
                        <div
                            class="flex gap-2 overflow-x-auto pb-1 scrollbar-none"
                        >
                            <button
                                v-for="category in categories"
                                :key="category"
                                type="button"
                                class="shrink-0 rounded-xl px-4 py-2.5 text-sm font-bold transition"
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

                    <!-- Empty State -->
                    <div
                        v-if="!filteredNews.length"
                        class="rounded-2xl border border-dashed border-slate-300 bg-white px-6 py-16 text-center"
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

                        <p class="mx-auto mt-2 max-w-md text-sm leading-6 text-slate-500">
                            Coba gunakan kata kunci lain atau pilih kategori
                            yang berbeda.
                        </p>
                    </div>

                    <template v-else>
                        <!-- Featured -->
                        <div
                            v-if="featuredNews"
                            class="group relative mb-10 overflow-hidden rounded-3xl border border-slate-200 bg-white shadow-sm"
                        >
                            <div
                                class="grid min-h-[420px] lg:grid-cols-2"
                            >
                                <!-- Image -->
                                <div
                                    class="relative min-h-[280px] overflow-hidden bg-slate-100 lg:min-h-full"
                                >
                                    <img
                                        v-if="featuredNews.image"
                                        :src="featuredNews.image"
                                        :alt="featuredNews.title"
                                        class="h-full w-full object-cover transition duration-700 group-hover:scale-105"
                                    />

                                    <div
                                        v-else
                                        class="absolute inset-0 flex items-center justify-center bg-gradient-to-br from-slate-950 via-blue-950 to-cyan-700"
                                    >
                                        <Newspaper
                                            class="h-20 w-20 text-white/20"
                                        />

                                        <div
                                            class="absolute inset-0 opacity-20"
                                            style="
                                                background-image:
                                                    linear-gradient(
                                                        rgba(
                                                            255,
                                                            255,
                                                            255,
                                                            0.16
                                                        )
                                                        1px,
                                                        transparent 1px
                                                    ),
                                                    linear-gradient(
                                                        90deg,
                                                        rgba(
                                                            255,
                                                            255,
                                                            255,
                                                            0.16
                                                        )
                                                        1px,
                                                        transparent 1px
                                                    );
                                                background-size: 32px 32px;
                                            "
                                        />
                                    </div>

                                    <div
                                        class="absolute left-5 top-5 rounded-full bg-white px-3 py-1.5 text-xs font-extrabold text-slate-950 shadow-sm"
                                    >
                                        Featured
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
                                        <Link
                                            :href="`/berita/${featuredNews.id}`"
                                            class="group/link inline-flex items-center gap-2 rounded-xl bg-slate-950 px-5 py-3 text-sm font-bold text-white transition hover:bg-blue-600"
                                        >
                                            Baca selengkapnya
                                            <ArrowRight
                                                class="h-4 w-4 transition-transform group-hover/link:translate-x-1"
                                            />
                                        </Link>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- News Grid -->
                        <div
                            v-if="otherNews.length"
                            class="grid gap-6 md:grid-cols-2 lg:grid-cols-3"
                        >
                            <article
                                v-for="item in otherNews"
                                :key="item.id"
                                class="group flex flex-col overflow-hidden rounded-2xl border border-slate-200 bg-white shadow-sm transition duration-300 hover:-translate-y-1 hover:shadow-xl hover:shadow-slate-200/50"
                            >
                                <!-- Image -->
                                <div
                                    class="relative aspect-[16/9] overflow-hidden bg-slate-100"
                                >
                                    <img
                                        v-if="item.image"
                                        :src="item.image"
                                        :alt="item.title"
                                        class="h-full w-full object-cover transition duration-500 group-hover:scale-105"
                                    />

                                    <div
                                        v-else
                                        class="absolute inset-0 flex items-center justify-center bg-gradient-to-br from-slate-900 via-blue-900 to-cyan-700"
                                    >
                                        <Newspaper
                                            class="h-12 w-12 text-white/20"
                                        />

                                        <div
                                            class="absolute inset-0 opacity-20"
                                            style="
                                                background-image:
                                                    linear-gradient(
                                                        rgba(
                                                            255,
                                                            255,
                                                            255,
                                                            0.16
                                                        )
                                                        1px,
                                                        transparent 1px
                                                    ),
                                                    linear-gradient(
                                                        90deg,
                                                        rgba(
                                                            255,
                                                            255,
                                                            255,
                                                            0.16
                                                        )
                                                        1px,
                                                        transparent 1px
                                                    );
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

                                <!-- Content -->
                                <div
                                    class="flex flex-1 flex-col p-6"
                                >
                                    <div
                                        class="flex items-center gap-2 text-xs font-semibold text-slate-400"
                                    >
                                        <CalendarDays class="h-3.5 w-3.5" />
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
                                        <Link
                                            :href="`/berita/${item.id}`"
                                            class="inline-flex items-center gap-2 text-sm font-extrabold text-slate-950 transition hover:text-blue-600"
                                        >
                                            Baca berita
                                            <ChevronRight
                                                class="h-4 w-4 transition-transform group-hover:translate-x-1"
                                            />
                                        </Link>
                                    </div>
                                </div>
                            </article>
                        </div>
                    </template>
                </div>
            </section>

            <!-- CTA -->
            <section class="relative overflow-hidden bg-slate-950">
                <div
                    class="pointer-events-none absolute -left-32 -top-32 h-80 w-80 rounded-full bg-blue-600/30 blur-3xl"
                />

                <div
                    class="pointer-events-none absolute -bottom-40 -right-20 h-96 w-96 rounded-full bg-cyan-500/20 blur-3xl"
                />

                <div
                    class="relative mx-auto max-w-7xl px-4 py-16 sm:px-6 sm:py-20 lg:px-8"
                >
                    <div
                        class="flex flex-col items-start justify-between gap-8 md:flex-row md:items-center"
                    >
                        <div class="max-w-2xl">
                            <div
                                class="mb-4 text-xs font-bold uppercase tracking-[0.16em] text-blue-400"
                            >
                                Organisasi Terintegrasi
                            </div>

                            <h2
                                class="text-3xl font-extrabold tracking-[-0.03em] text-white sm:text-4xl"
                            >
                                Bangun organisasi yang lebih terstruktur.
                            </h2>

                            <p
                                class="mt-4 text-sm leading-7 text-slate-400 sm:text-base"
                            >
                                Kelola struktur, kepengurusan, anggota, dan
                                aktivitas organisasi dalam satu sistem
                                terintegrasi.
                            </p>
                        </div>

                        <Link
                            href="/login"
                            class="inline-flex shrink-0 items-center gap-2 rounded-xl bg-white px-6 py-3.5 text-sm font-extrabold text-slate-950 transition hover:bg-blue-50"
                        >
                            Masuk ke sistem
                            <ArrowUpRight class="h-4 w-4" />
                        </Link>
                    </div>
                </div>
            </section>
        </main>

        <!-- Footer -->
        <footer class="border-t border-slate-800 bg-slate-950">
            <div
                class="mx-auto flex max-w-7xl flex-col gap-6 px-4 py-8 sm:px-6 md:flex-row md:items-center md:justify-between lg:px-8"
            >
                <div>
                    <div class="flex items-center gap-3">
                        <div
                            class="flex h-9 w-9 items-center justify-center rounded-lg bg-white text-slate-950"
                        >
                            <Network class="h-4 w-4" />
                        </div>

                        <div>
                            <div
                                class="text-sm font-extrabold text-white"
                            >
                                ORGANISASI
                            </div>

                            <div
                                class="text-[10px] font-semibold uppercase tracking-[0.16em] text-slate-500"
                            >
                                Management System
                            </div>
                        </div>
                    </div>

                    <p class="mt-4 text-xs text-slate-500">
                        Sistem manajemen organisasi multi-cabang.
                    </p>
                </div>

                <div
                    class="flex flex-wrap items-center gap-x-6 gap-y-3 text-xs font-semibold text-slate-500"
                >
                    <Link
                        href="/"
                        class="transition hover:text-white"
                    >
                        Beranda
                    </Link>

                    <Link
                        href="/berita"
                        class="transition hover:text-white"
                    >
                        Berita
                    </Link>

                    <Link
                        href="/login"
                        class="transition hover:text-white"
                    >
                        Login
                    </Link>
                </div>
            </div>

            <div
                class="border-t border-white/5 px-4 py-5 text-center text-xs text-slate-600"
            >
                © 2026 Organization Management System. All rights reserved.
            </div>
        </footer>
    </div>
</template>
