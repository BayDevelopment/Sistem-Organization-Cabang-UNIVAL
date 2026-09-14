<script setup lang="ts">
import { Head, Link } from '@inertiajs/vue3';
import {
    ArrowRight,
    ArrowUp,
    ArrowUpRight,
    Building2,
    CalendarDays,
    ChevronRight,
    Globe2,
    MapPin,
    Menu,
    Network,
    Newspaper,
    ShieldCheck,
    Users,
    X,
} from 'lucide-vue-next';
import {
    ComponentPublicInstance,
    onBeforeUnmount,
    onMounted,
    ref,
} from 'vue';

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
const showBackToTop = ref(false);
const pageLoaded = ref(false);

const revealedSections = ref<HTMLElement[]>([]);

const handleScroll = () => {
    showBackToTop.value = window.scrollY > 500;
};

const scrollToTop = () => {
    window.scrollTo({
        top: 0,
        behavior: 'smooth',
    });
};

let revealObserver: IntersectionObserver | null = null;

onMounted(() => {
    requestAnimationFrame(() => {
        pageLoaded.value = true;
    });

    window.addEventListener('scroll', handleScroll, {
        passive: true,
    });

    handleScroll();

    revealObserver = new IntersectionObserver(
        (entries) => {
            entries.forEach((entry) => {
                if (entry.isIntersecting) {
                    entry.target.classList.add('is-visible');
                    revealObserver?.unobserve(entry.target);
                }
            });
        },
        {
            threshold: 0.12,
            rootMargin: '0px 0px -60px 0px',
        },
    );

    revealedSections.value.forEach((element) => {
        revealObserver?.observe(element);
    });
});

onBeforeUnmount(() => {
    window.removeEventListener('scroll', handleScroll);

    revealObserver?.disconnect();
    revealObserver = null;
});

const setRevealRef = (element: Element | ComponentPublicInstance | null) => {
    if (element instanceof HTMLElement && !revealedSections.value.includes(element)) {
        revealedSections.value.push(element);

        if (revealObserver) {
            revealObserver.observe(element);
        }
    }
};

const stats = [
    {
        value: '1,284',
        label: 'Anggota Aktif',
        icon: Users,
    },
    {
        value: '42',
        label: 'Organisasi Cabang',
        icon: Building2,
    },
    {
        value: '8',
        label: 'Wilayah',
        icon: MapPin,
    },
    {
        value: '156',
        label: 'Kegiatan',
        icon: CalendarDays,
    },
];

const features = [
    {
        number: '01',
        title: 'Struktur Organisasi',
        description:
            'Kelola struktur organisasi bertingkat dari pusat hingga cabang secara terpusat dan terstruktur.',
        icon: Network,
    },
    {
        number: '02',
        title: 'Manajemen Anggota',
        description:
            'Kelola data anggota, status keanggotaan, riwayat, hingga kartu anggota digital.',
        icon: Users,
    },
    {
        number: '03',
        title: 'Kegiatan Organisasi',
        description:
            'Rencanakan kegiatan, kelola peserta, absensi, dokumentasi, dan laporan kegiatan.',
        icon: CalendarDays,
    },
    {
        number: '04',
        title: 'Laporan Terpusat',
        description:
            'Pantau perkembangan organisasi dan aktivitas cabang melalui laporan yang terintegrasi.',
        icon: Newspaper,
    },
];

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

const displayedNews = props.news.length
    ? props.news
    : dummyNews;
</script>

<template>
    <Head title="Organization Management System" />

    <div
        class="min-h-screen overflow-x-hidden bg-white font-sans text-slate-950"
        :class="pageLoaded ? 'page-loaded' : 'page-loading'"
    >
        <!-- =========================================================
        Navigation
        ========================================================== -->
        <header
            class="fixed inset-x-0 top-0 z-50 border-b border-white/10 bg-slate-950/80 text-white shadow-lg shadow-slate-950/5 backdrop-blur-xl transition-all duration-300"
        >
            <div
                class="mx-auto flex max-w-7xl items-center justify-between px-6 py-4 lg:px-8"
            >
                <Link
                    href="/"
                    class="flex items-center gap-3"
                >
                    <div
                        class="flex h-10 w-10 items-center justify-center rounded-xl bg-white text-slate-950 shadow-lg"
                    >
                        <Network class="h-5 w-5" />
                    </div>

                    <div>
                        <div class="text-sm font-bold tracking-wide">
                            ORGANIZATION
                        </div>

                        <div
                            class="text-[10px] font-medium uppercase tracking-[0.25em] text-white/60"
                        >
                            Management System
                        </div>
                    </div>
                </Link>

                <nav class="hidden items-center gap-8 md:flex">
                    <a
                        href="#tentang"
                        class="text-sm text-white/75 transition-colors duration-300 hover:text-white"
                    >
                        Tentang
                    </a>

                    <a
                        href="#fitur"
                        class="text-sm text-white/75 transition-colors duration-300 hover:text-white"
                    >
                        Fitur
                    </a>

                    <a
                        href="#kegiatan"
                        class="text-sm text-white/75 transition-colors duration-300 hover:text-white"
                    >
                        Kegiatan
                    </a>

                    <Link
                        href="/berita"
                        class="text-sm text-white/75 transition-colors duration-300 hover:text-white"
                    >
                        Berita
                    </Link>
                </nav>

                <div class="hidden md:block">
                    <Link
                        href="/login"
                        class="inline-flex items-center gap-2 rounded-full bg-white px-5 py-2.5 text-sm font-semibold text-slate-950 transition-all duration-300 hover:-translate-y-0.5 hover:bg-slate-100 hover:shadow-lg"
                    >
                        Masuk Sistem
                        <ArrowUpRight class="h-4 w-4" />
                    </Link>
                </div>

                <button
                    type="button"
                    aria-label="Toggle navigation"
                    class="rounded-lg border border-white/15 p-2 transition-all duration-300 hover:bg-white/10 md:hidden"
                    @click="mobileMenuOpen = !mobileMenuOpen"
                >
                    <Transition
                        mode="out-in"
                        enter-active-class="transition duration-200"
                        enter-from-class="scale-75 rotate-90 opacity-0"
                        enter-to-class="scale-100 rotate-0 opacity-100"
                        leave-active-class="transition duration-150"
                        leave-from-class="scale-100 rotate-0 opacity-100"
                        leave-to-class="scale-75 -rotate-90 opacity-0"
                    >
                        <X
                            v-if="mobileMenuOpen"
                            key="close"
                            class="h-5 w-5"
                        />

                        <Menu
                            v-else
                            key="menu"
                            class="h-5 w-5"
                        />
                    </Transition>
                </button>
            </div>

            <Transition
                enter-active-class="transition-all duration-300 ease-out"
                enter-from-class="-translate-y-3 opacity-0"
                enter-to-class="translate-y-0 opacity-100"
                leave-active-class="transition-all duration-200 ease-in"
                leave-from-class="translate-y-0 opacity-100"
                leave-to-class="-translate-y-3 opacity-0"
            >
                <div
                    v-if="mobileMenuOpen"
                    class="border-t border-white/10 bg-slate-950/95 px-6 py-5 backdrop-blur-xl md:hidden"
                >
                    <nav class="flex flex-col gap-4">
                        <a
                            href="#tentang"
                            class="text-white/80 transition-colors duration-300 hover:text-white"
                            @click="mobileMenuOpen = false"
                        >
                            Tentang
                        </a>

                        <a
                            href="#fitur"
                            class="text-white/80 transition-colors duration-300 hover:text-white"
                            @click="mobileMenuOpen = false"
                        >
                            Fitur
                        </a>

                        <a
                            href="#kegiatan"
                            class="text-white/80 transition-colors duration-300 hover:text-white"
                            @click="mobileMenuOpen = false"
                        >
                            Kegiatan
                        </a>

                        <Link
                            href="/berita"
                            class="text-white/80 transition-colors duration-300 hover:text-white"
                            @click="mobileMenuOpen = false"
                        >
                            Berita
                        </Link>

                        <Link
                            href="/login"
                            class="mt-2 rounded-xl bg-white px-4 py-3 text-center font-semibold text-slate-950 transition-all duration-300 hover:-translate-y-0.5 hover:bg-slate-100"
                        >
                            Masuk Sistem
                        </Link>
                    </nav>
                </div>
            </Transition>
        </header>

        <!-- =========================================================
        Hero
        ========================================================== -->
        <section
            class="relative isolate min-h-[760px] overflow-hidden bg-slate-950"
        >
            <div
                class="absolute inset-0 opacity-[0.08]"
                style="
                    background-image:
                        linear-gradient(rgba(255,255,255,.5) 1px, transparent 1px),
                        linear-gradient(90deg, rgba(255,255,255,.5) 1px, transparent 1px);
                    background-size: 64px 64px;
                "
            />

            <div
                class="absolute -right-40 -top-40 h-[600px] w-[600px] rounded-full bg-cyan-500/20 blur-3xl"
            />

            <div
                class="absolute -bottom-60 -left-40 h-[600px] w-[600px] rounded-full bg-blue-600/20 blur-3xl"
            />

            <div
                class="relative mx-auto flex min-h-[760px] max-w-7xl items-center px-6 pb-20 pt-32 lg:px-8"
            >
                <div class="max-w-4xl">
                    <div
                        class="hero-reveal hero-delay-1 mb-7 inline-flex items-center gap-2 rounded-full border border-white/10 bg-white/[0.06] px-4 py-2 text-sm text-white/70 backdrop-blur"
                    >
                        <span class="h-2 w-2 rounded-full bg-cyan-400" />
                        Platform Digital Organisasi Terintegrasi
                    </div>

                    <h1
                        class="hero-reveal hero-delay-2 max-w-5xl text-5xl font-bold leading-[1.02] tracking-tight text-white sm:text-6xl lg:text-8xl"
                    >
                        Menghubungkan

                        <span
                            class="bg-gradient-to-r from-cyan-300 via-blue-400 to-indigo-400 bg-clip-text text-transparent"
                        >
                            seluruh organisasi.
                        </span>
                    </h1>

                    <p
                        class="hero-reveal hero-delay-3 mt-7 max-w-2xl text-base leading-8 text-slate-300 sm:text-lg"
                    >
                        Satu platform untuk mengelola organisasi pusat hingga
                        cabang, anggota, pengurus, kegiatan, dokumen, surat,
                        dan laporan secara terstruktur.
                    </p>

                    <div
                        class="hero-reveal hero-delay-4 mt-9 flex flex-col gap-3 sm:flex-row"
                    >
                        <Link
                            href="/login"
                            class="group inline-flex items-center justify-center gap-2 rounded-full bg-white px-6 py-3.5 text-sm font-bold text-slate-950 transition-all duration-300 ease-out hover:-translate-y-1 hover:bg-slate-100 hover:shadow-xl hover:shadow-white/10"
                        >
                            Mulai Kelola Organisasi

                            <ArrowRight
                                class="h-4 w-4 transition-transform duration-300 group-hover:translate-x-1"
                            />
                        </Link>

                        <a
                            href="#tentang"
                            class="inline-flex items-center justify-center rounded-full border border-white/15 px-6 py-3.5 text-sm font-semibold text-white transition-all duration-300 hover:-translate-y-0.5 hover:border-white/30 hover:bg-white/10"
                        >
                            Pelajari Lebih Lanjut
                        </a>
                    </div>

                    <div
                        class="hero-reveal hero-delay-5 mt-12 flex flex-wrap items-center gap-x-8 gap-y-4 text-sm text-white/50"
                    >
                        <div class="flex items-center gap-2">
                            <ShieldCheck class="h-4 w-4 text-cyan-400" />
                            Akses berbasis role
                        </div>

                        <div class="flex items-center gap-2">
                            <Globe2 class="h-4 w-4 text-cyan-400" />
                            Multi-cabang
                        </div>

                        <div class="flex items-center gap-2">
                            <Network class="h-4 w-4 text-cyan-400" />
                            Hierarki terstruktur
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- =========================================================
        Stats
        ========================================================== -->
        <section
            ref="setRevealRef"
            class="reveal-on-scroll relative z-10 -mt-12 px-6 lg:px-8"
        >
            <div
                class="mx-auto grid max-w-7xl overflow-hidden rounded-3xl border border-slate-200 bg-white shadow-2xl shadow-slate-900/10 sm:grid-cols-2 lg:grid-cols-4"
            >
                <div
                    v-for="(stat, index) in stats"
                    :key="stat.label"
                    class="border-b border-slate-100 p-7 last:border-b-0 transition-colors duration-300 hover:bg-slate-50 sm:border-r sm:last:border-r-0 lg:border-b-0"
                    :style="{ transitionDelay: `${index * 80}ms` }"
                >
                    <component
                        :is="stat.icon"
                        class="mb-6 h-5 w-5 text-blue-600"
                    />

                    <div
                        class="text-3xl font-bold tracking-tight text-slate-950"
                    >
                        {{ stat.value }}
                    </div>

                    <div class="mt-1 text-sm text-slate-500">
                        {{ stat.label }}
                    </div>
                </div>
            </div>
        </section>

        <!-- =========================================================
        About
        ========================================================== -->
        <section
            id="tentang"
            ref="setRevealRef"
            class="reveal-on-scroll scroll-mt-24 px-6 py-28 lg:px-8"
        >
            <div
                class="mx-auto grid max-w-7xl gap-16 lg:grid-cols-[0.85fr_1.15fr] lg:items-center"
            >
                <div>
                    <p
                        class="text-sm font-bold uppercase tracking-[0.2em] text-blue-600"
                    >
                        Tentang Platform
                    </p>

                    <h2
                        class="mt-4 text-4xl font-bold tracking-tight text-slate-950 sm:text-5xl"
                    >
                        Satu sistem untuk organisasi yang terus berkembang.
                    </h2>
                </div>

                <div>
                    <p class="text-lg leading-8 text-slate-600">
                        Organization Management System dirancang untuk
                        menyederhanakan pengelolaan organisasi yang memiliki
                        banyak tingkatan dan cabang.
                    </p>

                    <p class="mt-5 leading-7 text-slate-500">
                        Setiap organisasi memiliki ruang pengelolaan
                        masing-masing, sementara pengurus pada tingkat yang
                        lebih tinggi tetap dapat memantau data sesuai
                        kewenangannya.
                    </p>

                    <div
                        class="mt-8 flex flex-wrap items-center gap-3 text-sm font-semibold text-slate-950"
                    >
                        Struktur terpusat
                        <ArrowRight class="h-4 w-4 text-blue-600" />
                        Akses sesuai kewenangan
                    </div>
                </div>
            </div>
        </section>

        <!-- =========================================================
        Organization Structure
        ========================================================== -->
        <section
            ref="setRevealRef"
            class="reveal-on-scroll bg-slate-50 px-6 py-24 lg:px-8"
        >
            <div class="mx-auto max-w-7xl">
                <div class="max-w-2xl">
                    <p
                        class="text-sm font-bold uppercase tracking-[0.2em] text-blue-600"
                    >
                        Struktur Organisasi
                    </p>

                    <h2
                        class="mt-4 text-4xl font-bold tracking-tight text-slate-950"
                    >
                        Dari pusat hingga unit terkecil.
                    </h2>

                    <p class="mt-5 leading-7 text-slate-500">
                        Struktur parent-child memungkinkan organisasi mengelola
                        ribuan cabang secara jelas dan terorganisir.
                    </p>
                </div>

                <div class="mt-14 overflow-x-auto pb-4">
                    <div
                        class="mx-auto flex min-w-[850px] flex-col items-center"
                    >
                        <div
                            class="rounded-2xl border border-slate-200 bg-white px-8 py-5 text-center shadow-sm transition-all duration-300 hover:-translate-y-1 hover:shadow-lg"
                        >
                            <div
                                class="text-xs font-semibold uppercase tracking-widest text-blue-600"
                            >
                                Level 01
                            </div>

                            <div class="mt-1 font-bold">
                                Pengurus Pusat
                            </div>
                        </div>

                        <div class="h-10 w-px bg-slate-300" />

                        <div class="h-px w-2/3 bg-slate-300" />

                        <div class="grid w-2/3 grid-cols-3">
                            <div class="flex flex-col items-center">
                                <div class="h-8 w-px bg-slate-300" />

                                <div
                                    class="rounded-2xl border border-slate-200 bg-white px-5 py-4 text-center shadow-sm transition-all duration-300 hover:-translate-y-1 hover:shadow-lg"
                                >
                                    <div class="text-xs text-blue-600">
                                        Level 02
                                    </div>

                                    <div class="mt-1 text-sm font-bold">
                                        Wilayah
                                    </div>
                                </div>
                            </div>

                            <div class="flex flex-col items-center">
                                <div class="h-8 w-px bg-slate-300" />

                                <div
                                    class="rounded-2xl border border-slate-200 bg-white px-5 py-4 text-center shadow-sm transition-all duration-300 hover:-translate-y-1 hover:shadow-lg"
                                >
                                    <div class="text-xs text-blue-600">
                                        Level 02
                                    </div>

                                    <div class="mt-1 text-sm font-bold">
                                        Daerah
                                    </div>
                                </div>
                            </div>

                            <div class="flex flex-col items-center">
                                <div class="h-8 w-px bg-slate-300" />

                                <div
                                    class="rounded-2xl border border-slate-200 bg-white px-5 py-4 text-center shadow-sm transition-all duration-300 hover:-translate-y-1 hover:shadow-lg"
                                >
                                    <div class="text-xs text-blue-600">
                                        Level 02
                                    </div>

                                    <div class="mt-1 text-sm font-bold">
                                        Wilayah Khusus
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div
                            class="mt-8 flex flex-wrap items-center justify-center gap-3"
                        >
                            <div
                                class="rounded-xl bg-slate-950 px-5 py-3 text-sm font-semibold text-white"
                            >
                                Pengurus Cabang
                            </div>

                            <ChevronRight
                                class="h-4 w-4 text-slate-400"
                            />

                            <div
                                class="rounded-xl border border-slate-200 bg-white px-5 py-3 text-sm font-semibold"
                            >
                                Ranting / Unit
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- =========================================================
        Features
        ========================================================== -->
        <section
            id="fitur"
            ref="setRevealRef"
            class="reveal-on-scroll scroll-mt-24 px-6 py-28 lg:px-8"
        >
            <div class="mx-auto max-w-7xl">
                <div
                    class="flex flex-col justify-between gap-6 md:flex-row md:items-end"
                >
                    <div class="max-w-2xl">
                        <p
                            class="text-sm font-bold uppercase tracking-[0.2em] text-blue-600"
                        >
                            Fitur Utama
                        </p>

                        <h2
                            class="mt-4 text-4xl font-bold tracking-tight text-slate-950 sm:text-5xl"
                        >
                            Semua yang dibutuhkan organisasi.
                        </h2>
                    </div>

                    <p class="max-w-md text-sm leading-6 text-slate-500">
                        Dirancang modular agar setiap bagian organisasi dapat
                        bekerja lebih efektif dalam satu sistem.
                    </p>
                </div>

                <div
                    class="mt-14 grid gap-px overflow-hidden rounded-3xl border border-slate-200 bg-slate-200 md:grid-cols-2"
                >
                    <article
                        v-for="feature in features"
                        :key="feature.number"
                        class="group bg-white p-8 transition-all duration-300 hover:bg-slate-950 hover:text-white"
                    >
                        <div class="flex items-start justify-between">
                            <component
                                :is="feature.icon"
                                class="h-6 w-6 text-blue-600"
                            />

                            <span class="text-xs font-bold text-slate-300">
                                {{ feature.number }}
                            </span>
                        </div>

                        <h3 class="mt-16 text-2xl font-bold">
                            {{ feature.title }}
                        </h3>

                        <p
                            class="mt-4 max-w-md leading-7 text-slate-500 transition-colors duration-300 group-hover:text-slate-400"
                        >
                            {{ feature.description }}
                        </p>

                        <div
                            class="mt-7 flex items-center gap-2 text-sm font-semibold"
                        >
                            Pelajari fitur

                            <ArrowRight
                                class="h-4 w-4 text-blue-600 transition-transform duration-300 group-hover:translate-x-1"
                            />
                        </div>
                    </article>
                </div>
            </div>
        </section>

        <!-- =========================================================
        News
        ========================================================== -->
        <section
            id="kegiatan"
            ref="setRevealRef"
            class="reveal-on-scroll scroll-mt-24 bg-slate-950 px-6 py-28 text-white lg:px-8"
        >
            <div class="mx-auto max-w-7xl">
                <div
                    class="flex flex-col justify-between gap-6 md:flex-row md:items-end"
                >
                    <div>
                        <p
                            class="text-sm font-bold uppercase tracking-[0.2em] text-cyan-400"
                        >
                            Informasi Terbaru
                        </p>

                        <h2
                            class="mt-4 text-4xl font-bold tracking-tight sm:text-5xl"
                        >
                            Berita & kegiatan.
                        </h2>
                    </div>

                    <Link
                        href="/berita"
                        class="group inline-flex items-center gap-2 text-sm font-semibold text-white/70 transition-colors duration-300 hover:text-white"
                    >
                        Lihat semua berita

                        <ArrowUpRight
                            class="h-4 w-4 transition-transform duration-300 group-hover:translate-x-1 group-hover:-translate-y-0.5"
                        />
                    </Link>
                </div>

                <div
                    v-if="displayedNews.length === 0"
                    class="mt-14 rounded-3xl border border-white/10 bg-white/[0.04] px-6 py-16 text-center"
                >
                    <Newspaper
                        class="mx-auto h-10 w-10 text-white/20"
                    />

                    <h3 class="mt-5 text-lg font-semibold">
                        Belum ada berita
                    </h3>

                    <p class="mt-2 text-sm text-white/40">
                        Informasi terbaru organisasi akan ditampilkan di sini.
                    </p>
                </div>

                <div
                    v-else
                    class="mt-14 grid gap-6 lg:grid-cols-3"
                >
                    <article
                        v-for="item in displayedNews.slice(0, 3)"
                        :key="item.id"
                        class="group overflow-hidden rounded-3xl border border-white/10 bg-white/[0.05] transition-all duration-300 hover:-translate-y-1 hover:border-white/20 hover:bg-white/[0.07] hover:shadow-2xl hover:shadow-black/20"
                    >
                        <div
                            class="relative aspect-[16/10] overflow-hidden bg-gradient-to-br from-blue-600 via-indigo-700 to-slate-900"
                        >
                            <img
                                v-if="item.image"
                                :src="item.image"
                                :alt="item.title"
                                class="h-full w-full object-cover transition duration-500 group-hover:scale-105"
                            />

                            <div
                                v-else
                                class="absolute inset-0 flex items-center justify-center"
                            >
                                <Newspaper
                                    class="h-12 w-12 text-white/20"
                                />
                            </div>

                            <div
                                class="absolute left-5 top-5 rounded-full bg-white/10 px-3 py-1.5 text-xs font-semibold backdrop-blur"
                            >
                                {{ item.category }}
                            </div>
                        </div>

                        <div class="p-7">
                            <div class="text-xs text-white/40">
                                {{ item.date }}
                            </div>

                            <h3
                                class="mt-3 text-xl font-bold leading-snug"
                            >
                                {{ item.title }}
                            </h3>

                            <p
                                class="mt-3 line-clamp-2 text-sm leading-6 text-white/50"
                            >
                                {{ item.excerpt }}
                            </p>

                            <Link
                                href="/berita"
                                class="group/link mt-6 inline-flex items-center gap-2 text-sm font-semibold text-cyan-400 transition-colors duration-300 hover:text-cyan-300"
                            >
                                Baca selengkapnya

                                <ArrowRight
                                    class="h-4 w-4 transition-transform duration-300 group-hover/link:translate-x-1"
                                />
                            </Link>
                        </div>
                    </article>
                </div>
            </div>
        </section>

        <!-- =========================================================
        CTA
        ========================================================== -->
        <section
            ref="setRevealRef"
            class="reveal-on-scroll px-6 py-24 lg:px-8"
        >
            <div
                class="mx-auto max-w-7xl overflow-hidden rounded-[2rem] bg-gradient-to-br from-blue-600 via-indigo-600 to-slate-950 px-8 py-16 text-white sm:px-14"
            >
                <div class="max-w-3xl">
                    <p
                        class="text-sm font-semibold uppercase tracking-[0.2em] text-white/60"
                    >
                        Mulai Sekarang
                    </p>

                    <h2
                        class="mt-4 text-4xl font-bold tracking-tight sm:text-5xl"
                    >
                        Bangun organisasi yang lebih terhubung.
                    </h2>

                    <p
                        class="mt-5 max-w-2xl leading-7 text-white/70"
                    >
                        Kelola seluruh struktur, anggota, kegiatan, dan
                        administrasi organisasi dari satu platform.
                    </p>

                    <Link
                        href="/login"
                        class="group mt-8 inline-flex items-center gap-2 rounded-full bg-white px-6 py-3.5 text-sm font-bold text-slate-950 transition-all duration-300 hover:-translate-y-1 hover:bg-slate-100 hover:shadow-xl"
                    >
                        Masuk ke Sistem

                        <ArrowRight
                            class="h-4 w-4 transition-transform duration-300 group-hover:translate-x-1"
                        />
                    </Link>
                </div>
            </div>
        </section>

        <!-- =========================================================
        Footer
        ========================================================== -->
        <footer class="border-t border-slate-200 px-6 py-10 lg:px-8">
            <div
                class="mx-auto flex max-w-7xl flex-col justify-between gap-6 md:flex-row md:items-center"
            >
                <div class="flex items-center gap-3">
                    <div
                        class="flex h-9 w-9 items-center justify-center rounded-lg bg-slate-950 text-white"
                    >
                        <Network class="h-4 w-4" />
                    </div>

                    <div>
                        <div class="text-sm font-bold">
                            ORGANIZATION
                        </div>

                        <div
                            class="text-[10px] uppercase tracking-[0.2em] text-slate-400"
                        >
                            Management System
                        </div>
                    </div>
                </div>

                <div class="text-sm text-slate-400">
                    © {{ new Date().getFullYear() }}
                    Organization Management System.
                </div>

                <div class="flex gap-6 text-sm text-slate-500">
                    <a
                        href="#"
                        class="transition-colors duration-300 hover:text-slate-950"
                    >
                        Privasi
                    </a>

                    <a
                        href="#"
                        class="transition-colors duration-300 hover:text-slate-950"
                    >
                        Ketentuan
                    </a>
                </div>
            </div>
        </footer>

        <!-- =========================================================
        Back To Top
        ========================================================== -->
        <Transition
            enter-active-class="transition-all duration-300 ease-out"
            enter-from-class="translate-y-4 scale-75 opacity-0"
            enter-to-class="translate-y-0 scale-100 opacity-100"
            leave-active-class="transition-all duration-200 ease-in"
            leave-from-class="translate-y-0 scale-100 opacity-100"
            leave-to-class="translate-y-4 scale-75 opacity-0"
        >
            <button
                v-if="showBackToTop"
                type="button"
                aria-label="Kembali ke atas"
                class="group fixed bottom-6 right-6 z-40 flex h-12 w-12 items-center justify-center rounded-full border border-white/10 bg-slate-950/90 text-white shadow-xl shadow-slate-950/20 backdrop-blur-xl transition-all duration-300 hover:-translate-y-1 hover:bg-blue-600 hover:shadow-blue-600/25 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2"
                @click="scrollToTop"
            >
                <ArrowUp
                    class="h-5 w-5 transition-transform duration-300 group-hover:-translate-y-0.5"
                />
            </button>
        </Transition>
    </div>
</template>

<style scoped>
.page-loading {
    opacity: 0;
}

.page-loaded {
    opacity: 1;
    transition: opacity 700ms ease;
}

.hero-reveal {
    opacity: 0;
    transform: translateY(24px);
    animation: heroReveal 800ms cubic-bezier(0.22, 1, 0.36, 1) forwards;
}

.hero-delay-1 {
    animation-delay: 100ms;
}

.hero-delay-2 {
    animation-delay: 220ms;
}

.hero-delay-3 {
    animation-delay: 340ms;
}

.hero-delay-4 {
    animation-delay: 460ms;
}

.hero-delay-5 {
    animation-delay: 580ms;
}

.reveal-on-scroll {
    opacity: 0;
    transform: translateY(32px);
    transition:
        opacity 800ms cubic-bezier(0.22, 1, 0.36, 1),
        transform 800ms cubic-bezier(0.22, 1, 0.36, 1);
}

.reveal-on-scroll.is-visible {
    opacity: 1;
    transform: translateY(0);
}

@keyframes heroReveal {
    from {
        opacity: 0;
        transform: translateY(24px);
    }

    to {
        opacity: 1;
        transform: translateY(0);
    }
}

@media (prefers-reduced-motion: reduce) {
    .page-loading,
    .page-loaded,
    .hero-reveal,
    .reveal-on-scroll {
        opacity: 1;
        transform: none;
        animation: none;
        transition: none;
    }
}
</style>
