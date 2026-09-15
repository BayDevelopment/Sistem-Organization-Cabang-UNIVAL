<script setup lang="ts">
import { Head, Link } from '@inertiajs/vue3';
import {
    ArrowRight,
    ArrowUp,
    ArrowUpRight,
    Award,
    Building2,
    CalendarDays,
    ChevronRight,
    Landmark,
    Mail,
    MapPin,
    Menu,
    Network,
    Newspaper,
    Phone,
    ShieldCheck,
    Users,
    X,
} from 'lucide-vue-next';
import {
    nextTick,
    onBeforeUnmount,
    onMounted,
    ref,
    type ComponentPublicInstance,
} from 'vue';

/*
|--------------------------------------------------------------------------
| Types
|--------------------------------------------------------------------------
*/
interface StatItem {
    value: string;
    label: string;
}

interface PengurusItem {
    name: string;
    position: string;
    division: string;
}

interface SubOrgItem {
    name: string;
    type: string;
    members: number;
}

interface NewsItem {
    id: number;
    title: string;
    excerpt: string;
    category: string;
    date: string;
    image?: string | null;
}

interface Organisasi {
    name: string;
    parent: string;
    region: string;
    established: string;
    status: string;
    address: string;
    phone: string;
    email: string;
    description: string;
    stats: StatItem[];
}

/*
|--------------------------------------------------------------------------
| Props
|--------------------------------------------------------------------------
*/
const props = withDefaults(
    defineProps<{
        organisasi?: Organisasi;
        pengurus?: PengurusItem[];
        subOrganisasi?: SubOrgItem[];
        news?: NewsItem[];
    }>(),
    {
        organisasi: undefined,
        pengurus: () => [],
        subOrganisasi: () => [],
        news: () => [],
    },
);

/*
|--------------------------------------------------------------------------
| Dummy data (dipakai sebelum data cabang terhubung ke database)
|--------------------------------------------------------------------------
*/
const dummyOrganisasi: Organisasi = {
    name: 'Pengurus Cabang Jakarta Selatan',
    parent: 'Pengurus Wilayah DKI Jakarta',
    region: 'DKI Jakarta',
    established: '2010',
    status: 'Aktif',
    address: 'Jl. Melati Raya No. 45, Kebayoran Baru, Jakarta Selatan',
    phone: '(021) 555-0192',
    email: 'cabang.jaksel@organisasi.or.id',
    description:
        'Pengurus Cabang Jakarta Selatan membawahi seluruh ranting dan unit di wilayah kerja Jakarta Selatan. Cabang ini berperan menjalankan program kerja wilayah, membina ranting, serta menjadi penghubung antara anggota di tingkat akar rumput dengan pengurus wilayah dan pusat.',
    stats: [
        { value: '328', label: 'Anggota Aktif' },
        { value: '12', label: 'Ranting' },
        { value: '34', label: 'Unit' },
        { value: '56', label: 'Kegiatan Tahun Ini' },
    ],
};

const dummyPengurus: PengurusItem[] = [
    { name: 'Ahmad Fauzan', position: 'Ketua Cabang', division: 'Pimpinan' },
    {
        name: 'Siti Rahmawati',
        position: 'Wakil Ketua Cabang',
        division: 'Pimpinan',
    },
    {
        name: 'Budi Santoso',
        position: 'Sekretaris Cabang',
        division: 'Kesekretariatan',
    },
    {
        name: 'Dewi Anggraini',
        position: 'Bendahara Cabang',
        division: 'Keuangan',
    },
    {
        name: 'Rizal Hakim',
        position: 'Kepala Bidang Organisasi',
        division: 'Organisasi',
    },
    {
        name: 'Maya Puspita',
        position: 'Kepala Bidang Humas',
        division: 'Hubungan Masyarakat',
    },
];

const dummySubOrganisasi: SubOrgItem[] = [
    { name: 'Ranting Kebayoran Baru', type: 'Ranting', members: 48 },
    { name: 'Ranting Cilandak', type: 'Ranting', members: 36 },
    { name: 'Ranting Pasar Minggu', type: 'Ranting', members: 41 },
    { name: 'Ranting Tebet', type: 'Ranting', members: 29 },
    { name: 'Unit Pendidikan', type: 'Unit', members: 22 },
    { name: 'Unit Sosial & Kemanusiaan', type: 'Unit', members: 18 },
];

const dummyNews: NewsItem[] = [
    {
        id: 1,
        title: 'Pelantikan Pengurus Ranting Se-Jakarta Selatan',
        excerpt:
            'Pengurus cabang melantik jajaran pengurus ranting baru periode 2026–2029 sebagai penguatan struktur di tingkat akar rumput.',
        category: 'Kegiatan',
        date: '10 September 2026',
        image: null,
    },
    {
        id: 2,
        title: 'Rapat Kerja Cabang Susun Program Tahunan',
        excerpt:
            'Rapat kerja cabang membahas evaluasi program tahun berjalan dan menyusun agenda kegiatan untuk tahun mendatang.',
        category: 'Organisasi',
        date: '2 September 2026',
        image: null,
    },
    {
        id: 3,
        title: 'Bakti Sosial di Wilayah Kebayoran Baru',
        excerpt:
            'Unit Sosial bersama ranting setempat menggelar kegiatan bakti sosial untuk warga terdampak di wilayah kerja cabang.',
        category: 'Sosial',
        date: '25 Agustus 2026',
        image: null,
    },
];

const org = props.organisasi ?? dummyOrganisasi;
const displayedPengurus = props.pengurus.length
    ? props.pengurus
    : dummyPengurus;
const displayedSubOrg = props.subOrganisasi.length
    ? props.subOrganisasi
    : dummySubOrganisasi;
const displayedNews = props.news.length ? props.news : dummyNews;

/*
|--------------------------------------------------------------------------
| UI state
|--------------------------------------------------------------------------
*/
const mobileMenuOpen = ref(false);
const showBackToTop = ref(false);
const pageLoaded = ref(false);
const revealObserver = ref<IntersectionObserver | null>(null);
const revealElements = new Set<HTMLElement>();

function handleScroll() {
    showBackToTop.value = window.scrollY > 500;
}

function scrollToTop() {
    window.scrollTo({ top: 0, behavior: 'smooth' });
}

function setRevealRef(
    element: Element | ComponentPublicInstance | null,
) {
    if (!(element instanceof HTMLElement)) {
        return;
    }

    revealElements.add(element);

    if (!element.classList.contains('reveal-ready')) {
        element.classList.add('reveal-ready');
    }

    if (revealObserver.value) {
        revealObserver.value.observe(element);
    }
}

function revealAllSections() {
    revealElements.forEach((element) => element.classList.add('is-visible'));
}

function revealVisibleSections() {
    revealElements.forEach((element) => {
        const rect = element.getBoundingClientRect();

        if (rect.top < window.innerHeight * 0.92 && rect.bottom > 0) {
            element.classList.add('is-visible');
        }
    });
}

onMounted(async () => {
    window.addEventListener('scroll', handleScroll, { passive: true });
    handleScroll();

    await nextTick();

    requestAnimationFrame(() => {
        pageLoaded.value = true;
    });

    const reducedMotion = window.matchMedia(
        '(prefers-reduced-motion: reduce)',
    ).matches;

    if (reducedMotion || !('IntersectionObserver' in window)) {
        revealAllSections();
        return;
    }

    revealObserver.value = new IntersectionObserver(
        (entries) => {
            entries.forEach((entry) => {
                if (entry.isIntersecting) {
                    entry.target.classList.add('is-visible');
                    revealObserver.value?.unobserve(entry.target);
                }
            });
        },
        { threshold: 0.05, rootMargin: '0px 0px -50px 0px' },
    );

    revealElements.forEach((element) => revealObserver.value?.observe(element));

    requestAnimationFrame(() => revealVisibleSections());
});

onBeforeUnmount(() => {
    window.removeEventListener('scroll', handleScroll);
    revealObserver.value?.disconnect();
    revealElements.clear();
});
</script>

<template>
    <Head :title="org.name" />

    <div
        class="min-h-screen overflow-x-hidden bg-white font-sans text-slate-950"
        :class="pageLoaded ? 'page-loaded' : 'page-loading'"
    >
        <!-- Header -->
        <header
            class="fixed inset-x-0 top-0 z-50 border-b border-white/10 bg-slate-950/90 backdrop-blur-xl"
        >
            <div
                class="mx-auto flex h-20 max-w-7xl items-center justify-between px-5 sm:px-6 lg:px-8"
            >
                <Link href="/" class="group flex items-center gap-3">
                    <div
                        class="flex h-10 w-10 items-center justify-center rounded-xl bg-white text-sm font-black text-slate-950 transition-transform duration-300 group-hover:rotate-3"
                    >
                        OM
                    </div>
                    <div class="hidden sm:block">
                        <div class="text-[11px] font-bold tracking-[0.28em] text-white">
                            ORGANIZATION
                        </div>
                        <div class="text-[9px] font-medium tracking-[0.22em] text-slate-400">
                            MANAGEMENT SYSTEM
                        </div>
                    </div>
                </Link>

                <nav class="hidden items-center gap-8 lg:flex">
                    <Link href="/" class="text-sm font-medium text-slate-300 transition hover:text-white">
                        Beranda
                    </Link>
                    <a href="#profil" class="text-sm font-medium text-slate-300 transition hover:text-white">
                        Profil
                    </a>
                    <a href="#struktur" class="text-sm font-medium text-slate-300 transition hover:text-white">
                        Struktur
                    </a>
                    <a href="#ranting" class="text-sm font-medium text-slate-300 transition hover:text-white">
                        Ranting & Unit
                    </a>
                    <a href="#kegiatan" class="text-sm font-medium text-slate-300 transition hover:text-white">
                        Kegiatan
                    </a>
                    <Link href="/berita" class="text-sm font-medium text-slate-300 transition hover:text-white">
                        Berita
                    </Link>
                </nav>

                <div class="hidden items-center gap-3 lg:flex">
                    <Link
                        href="/login"
                        class="rounded-full border border-white/15 px-5 py-2.5 text-sm font-semibold text-white transition hover:border-white/30 hover:bg-white/10"
                    >
                        Masuk
                    </Link>
                </div>

                <button
                    type="button"
                    class="flex h-11 w-11 items-center justify-center rounded-xl border border-white/10 text-white lg:hidden"
                    aria-label="Toggle menu"
                    @click="mobileMenuOpen = !mobileMenuOpen"
                >
                    <X v-if="mobileMenuOpen" class="h-5 w-5" />
                    <Menu v-else class="h-5 w-5" />
                </button>
            </div>

            <Transition
                enter-active-class="transition duration-200 ease-out"
                enter-from-class="opacity-0 -translate-y-2"
                enter-to-class="opacity-100 translate-y-0"
                leave-active-class="transition duration-150 ease-in"
                leave-from-class="opacity-100 translate-y-0"
                leave-to-class="opacity-0 -translate-y-2"
            >
                <div v-if="mobileMenuOpen" class="border-t border-white/10 bg-slate-950 px-5 py-5 lg:hidden">
                    <nav class="flex flex-col gap-2">
                        <Link href="/" class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 hover:bg-white/5 hover:text-white" @click="mobileMenuOpen = false">
                            Beranda
                        </Link>
                        <a href="#profil" class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 hover:bg-white/5 hover:text-white" @click="mobileMenuOpen = false">
                            Profil
                        </a>
                        <a href="#struktur" class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 hover:bg-white/5 hover:text-white" @click="mobileMenuOpen = false">
                            Struktur
                        </a>
                        <a href="#ranting" class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 hover:bg-white/5 hover:text-white" @click="mobileMenuOpen = false">
                            Ranting & Unit
                        </a>
                        <a href="#kegiatan" class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 hover:bg-white/5 hover:text-white" @click="mobileMenuOpen = false">
                            Kegiatan
                        </a>
                        <Link href="/berita" class="rounded-xl px-4 py-3 text-sm font-medium text-slate-300 hover:bg-white/5 hover:text-white" @click="mobileMenuOpen = false">
                            Berita
                        </Link>
                        <Link href="/login" class="mt-3 rounded-xl bg-white px-4 py-3 text-center text-sm font-bold text-slate-950" @click="mobileMenuOpen = false">
                            Masuk ke Sistem
                        </Link>
                    </nav>
                </div>
            </Transition>
        </header>

        <main>
            <!-- Hero -->
            <section class="relative min-h-[90vh] overflow-hidden bg-slate-950 pt-20 text-white">
                <div class="absolute inset-0 overflow-hidden">
                    <div class="absolute -left-40 top-20 h-96 w-96 rounded-full bg-cyan-400/20 blur-3xl blob blob-one" />
                    <div class="absolute right-[-12rem] top-32 h-[34rem] w-[34rem] rounded-full bg-blue-500/20 blur-3xl blob blob-two" />
                    <div class="absolute bottom-[-12rem] left-1/3 h-[30rem] w-[30rem] rounded-full bg-violet-500/15 blur-3xl blob blob-three" />
                </div>

                <div
                    class="absolute inset-0 opacity-[0.06]"
                    style="
                        background-image:
                            linear-gradient(rgba(255,255,255,.8) 1px, transparent 1px),
                            linear-gradient(90deg, rgba(255,255,255,.8) 1px, transparent 1px);
                        background-size: 44px 44px;
                    "
                />

                <div class="relative mx-auto flex min-h-[calc(90vh-5rem)] max-w-7xl items-center px-5 py-16 sm:px-6 lg:px-8">
                    <div class="grid w-full items-center gap-16 lg:grid-cols-[1.05fr_.95fr]">
                        <div>
                            <div class="hero-reveal inline-flex items-center gap-2 rounded-full border border-white/10 bg-white/5 px-4 py-2 text-xs font-semibold text-slate-300 backdrop-blur">
                                <span class="h-2 w-2 rounded-full bg-emerald-400" />
                                {{ org.status }} &middot; {{ org.parent }}
                            </div>

                            <h1 class="hero-reveal hero-delay-1 mt-7 max-w-4xl text-4xl font-black leading-[1.02] tracking-tight sm:text-5xl lg:text-6xl">
                                {{ org.name }}
                            </h1>

                            <p class="hero-reveal hero-delay-2 mt-7 max-w-2xl text-base leading-8 text-slate-400 sm:text-lg">
                                {{ org.description }}
                            </p>

                            <div class="hero-reveal hero-delay-3 mt-8 flex flex-col gap-3 text-sm text-slate-300 sm:flex-row sm:flex-wrap sm:gap-6">
                                <div class="flex items-center gap-2">
                                    <MapPin class="h-4 w-4 text-cyan-300" />
                                    {{ org.address }}
                                </div>
                                <div class="flex items-center gap-2">
                                    <Phone class="h-4 w-4 text-cyan-300" />
                                    {{ org.phone }}
                                </div>
                                <div class="flex items-center gap-2">
                                    <Mail class="h-4 w-4 text-cyan-300" />
                                    {{ org.email }}
                                </div>
                            </div>

                            <div class="hero-reveal hero-delay-4 mt-9 flex flex-col gap-3 sm:flex-row">
                                <a
                                    href="#struktur"
                                    class="group inline-flex items-center justify-center gap-2 rounded-full bg-white px-7 py-3.5 text-sm font-bold text-slate-950 transition duration-300 hover:-translate-y-0.5 hover:bg-slate-200"
                                >
                                    Lihat Struktur Cabang
                                    <ArrowRight class="h-4 w-4 transition-transform group-hover:translate-x-1" />
                                </a>
                                <a
                                    href="#kegiatan"
                                    class="inline-flex items-center justify-center gap-2 rounded-full border border-white/15 px-7 py-3.5 text-sm font-bold text-white transition duration-300 hover:-translate-y-0.5 hover:bg-white/5"
                                >
                                    Kegiatan Cabang
                                </a>
                            </div>
                        </div>

                        <div class="hero-reveal hero-delay-5 relative hidden lg:block">
                            <div class="absolute -inset-10 rounded-full bg-blue-500/10 blur-3xl" />

                            <div class="relative rounded-[2rem] border border-white/10 bg-white/[0.04] p-6 shadow-2xl backdrop-blur-xl">
                                <div class="flex items-center justify-between border-b border-white/10 pb-5">
                                    <div>
                                        <p class="text-[10px] font-bold uppercase tracking-[0.25em] text-slate-500">
                                            Ringkasan Cabang
                                        </p>
                                        <h2 class="mt-2 text-xl font-bold">Data Singkat</h2>
                                    </div>
                                    <div class="flex h-10 w-10 items-center justify-center rounded-xl bg-white text-slate-950">
                                        <Landmark class="h-5 w-5" />
                                    </div>
                                </div>

                                <div class="mt-6 space-y-4">
                                    <div class="flex items-center justify-between rounded-xl border border-white/10 bg-white/[0.03] p-4">
                                        <span class="text-sm text-slate-400">Berdiri sejak</span>
                                        <span class="text-sm font-bold text-white">{{ org.established }}</span>
                                    </div>
                                    <div class="flex items-center justify-between rounded-xl border border-white/10 bg-white/[0.03] p-4">
                                        <span class="text-sm text-slate-400">Wilayah kerja</span>
                                        <span class="text-sm font-bold text-white">{{ org.region }}</span>
                                    </div>
                                    <div class="flex items-center justify-between rounded-xl border border-white/10 bg-white/[0.03] p-4">
                                        <span class="text-sm text-slate-400">Induk organisasi</span>
                                        <span class="text-sm font-bold text-white">{{ org.parent }}</span>
                                    </div>

                                    <div class="grid grid-cols-2 gap-3 pt-1">
                                        <div
                                            v-for="stat in org.stats.slice(0, 4)"
                                            :key="stat.label"
                                            class="rounded-xl border border-cyan-300/15 bg-cyan-300/5 p-4 text-center"
                                        >
                                            <div class="text-xl font-black text-white">{{ stat.value }}</div>
                                            <div class="mt-1 text-[11px] text-slate-400">{{ stat.label }}</div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Stats bar -->
            <section class="relative overflow-hidden border-b border-slate-200 bg-white">
                <div class="absolute -right-32 top-10 h-72 w-72 rounded-full bg-cyan-100 blur-3xl blob blob-two" />

                <div
                    ref="setRevealRef"
                    class="reveal-on-scroll relative mx-auto grid max-w-7xl grid-cols-2 divide-x divide-slate-200 sm:grid-cols-4 sm:divide-x px-5 sm:px-6 lg:px-8"
                >
                    <div v-for="stat in org.stats" :key="stat.label" class="px-4 py-10 text-center sm:px-6 lg:py-12">
                        <div class="mt-1 text-3xl font-black tracking-tight text-slate-950 sm:text-4xl">
                            {{ stat.value }}
                        </div>
                        <div class="mt-1 text-xs font-medium text-slate-500 sm:text-sm">
                            {{ stat.label }}
                        </div>
                    </div>
                </div>
            </section>

            <!-- Profil -->
            <section id="profil" class="relative overflow-hidden bg-slate-50 py-24 sm:py-32">
                <div class="absolute -left-40 top-10 h-96 w-96 rounded-full bg-blue-200/40 blur-3xl blob blob-one" />
                <div class="absolute -right-40 bottom-0 h-[30rem] w-[30rem] rounded-full bg-violet-200/30 blur-3xl blob blob-three" />

                <div ref="setRevealRef" class="reveal-on-scroll relative mx-auto max-w-7xl px-5 sm:px-6 lg:px-8">
                    <div class="grid gap-14 lg:grid-cols-[.85fr_1.15fr] lg:items-start">
                        <div>
                            <span class="inline-flex rounded-full border border-blue-200 bg-blue-50 px-4 py-2 text-xs font-bold uppercase tracking-[0.2em] text-blue-600">
                                Profil Cabang
                            </span>
                            <h2 class="mt-6 text-4xl font-black tracking-tight text-slate-950 sm:text-5xl">
                                Mengenal {{ org.name }}.
                            </h2>
                        </div>

                        <div>
                            <p class="text-lg leading-8 text-slate-600">
                                {{ org.description }}
                            </p>

                            <div class="mt-9 grid gap-4 sm:grid-cols-2">
                                <div class="rounded-2xl border border-slate-200 bg-white p-5 shadow-sm">
                                    <div class="flex h-10 w-10 items-center justify-center rounded-xl bg-slate-950 text-white">
                                        <CalendarDays class="h-5 w-5" />
                                    </div>
                                    <h3 class="mt-4 font-bold text-slate-950">Berdiri Sejak</h3>
                                    <p class="mt-2 text-sm leading-6 text-slate-500">{{ org.established }}</p>
                                </div>
                                <div class="rounded-2xl border border-slate-200 bg-white p-5 shadow-sm">
                                    <div class="flex h-10 w-10 items-center justify-center rounded-xl bg-slate-950 text-white">
                                        <MapPin class="h-5 w-5" />
                                    </div>
                                    <h3 class="mt-4 font-bold text-slate-950">Wilayah Kerja</h3>
                                    <p class="mt-2 text-sm leading-6 text-slate-500">{{ org.region }}</p>
                                </div>
                                <div class="rounded-2xl border border-slate-200 bg-white p-5 shadow-sm">
                                    <div class="flex h-10 w-10 items-center justify-center rounded-xl bg-slate-950 text-white">
                                        <Network class="h-5 w-5" />
                                    </div>
                                    <h3 class="mt-4 font-bold text-slate-950">Induk Organisasi</h3>
                                    <p class="mt-2 text-sm leading-6 text-slate-500">{{ org.parent }}</p>
                                </div>
                                <div class="rounded-2xl border border-slate-200 bg-white p-5 shadow-sm">
                                    <div class="flex h-10 w-10 items-center justify-center rounded-xl bg-slate-950 text-white">
                                        <ShieldCheck class="h-5 w-5" />
                                    </div>
                                    <h3 class="mt-4 font-bold text-slate-950">Status</h3>
                                    <p class="mt-2 text-sm leading-6 text-slate-500">{{ org.status }}</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Struktur Kepengurusan -->
            <section id="struktur" class="relative overflow-hidden bg-white py-24 sm:py-32">
                <div class="absolute right-[-12rem] top-20 h-[30rem] w-[30rem] rounded-full bg-cyan-100/70 blur-3xl blob blob-two" />
                <div class="absolute bottom-[-10rem] left-[-10rem] h-[28rem] w-[28rem] rounded-full bg-blue-100/60 blur-3xl blob blob-one" />

                <div ref="setRevealRef" class="reveal-on-scroll relative mx-auto max-w-7xl px-5 sm:px-6 lg:px-8">
                    <div class="mx-auto max-w-3xl text-center">
                        <span class="inline-flex rounded-full border border-slate-200 bg-slate-50 px-4 py-2 text-xs font-bold uppercase tracking-[0.2em] text-slate-600">
                            Struktur Kepengurusan
                        </span>
                        <h2 class="mt-6 text-4xl font-black tracking-tight text-slate-950 sm:text-5xl">
                            Pengurus Cabang periode berjalan.
                        </h2>
                        <p class="mt-5 text-base leading-8 text-slate-500 sm:text-lg">
                            Jajaran pengurus yang bertanggung jawab menjalankan roda organisasi di tingkat cabang.
                        </p>
                    </div>

                    <div class="mt-16 grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
                        <div
                            v-for="person in displayedPengurus"
                            :key="person.name"
                            class="rounded-2xl border border-slate-200 bg-white p-6 shadow-sm transition duration-300 hover:-translate-y-1 hover:shadow-lg"
                        >
                            <div class="flex h-12 w-12 items-center justify-center rounded-2xl bg-slate-950 text-sm font-black text-white">
                                {{ person.name.split(' ').map((n) => n[0]).slice(0, 2).join('') }}
                            </div>
                            <h3 class="mt-5 font-bold text-slate-950">{{ person.name }}</h3>
                            <p class="mt-1 text-sm font-semibold text-blue-600">{{ person.position }}</p>
                            <p class="mt-1 text-xs text-slate-500">{{ person.division }}</p>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Ranting & Unit -->
            <section id="ranting" class="relative overflow-hidden bg-slate-950 py-24 text-white sm:py-32">
                <div class="absolute -left-48 top-20 h-[32rem] w-[32rem] rounded-full bg-blue-500/15 blur-3xl blob blob-one" />
                <div class="absolute right-[-12rem] top-[-8rem] h-[34rem] w-[34rem] rounded-full bg-violet-500/15 blur-3xl blob blob-two" />

                <div ref="setRevealRef" class="reveal-on-scroll relative mx-auto max-w-7xl px-5 sm:px-6 lg:px-8">
                    <div class="flex flex-col justify-between gap-8 lg:flex-row lg:items-end">
                        <div class="max-w-3xl">
                            <span class="inline-flex rounded-full border border-white/10 bg-white/5 px-4 py-2 text-xs font-bold uppercase tracking-[0.2em] text-slate-400">
                                Ranting & Unit
                            </span>
                            <h2 class="mt-6 text-4xl font-black tracking-tight sm:text-5xl">
                                Jaringan di bawah cabang ini.
                            </h2>
                            <p class="mt-5 text-base leading-8 text-slate-400 sm:text-lg">
                                Ranting dan unit yang berada dalam koordinasi langsung Pengurus Cabang.
                            </p>
                        </div>

                        <div class="hidden h-20 w-20 items-center justify-center rounded-3xl border border-white/10 bg-white/5 lg:flex">
                            <Award class="h-9 w-9 text-cyan-300" />
                        </div>
                    </div>

                    <div class="mt-14 grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
                        <article
                            v-for="item in displayedSubOrg"
                            :key="item.name"
                            class="group rounded-3xl border border-white/10 bg-white/[0.04] p-7 transition duration-500 hover:-translate-y-1 hover:bg-white/[0.07]"
                        >
                            <div class="flex items-center justify-between">
                                <div class="flex h-12 w-12 items-center justify-center rounded-2xl bg-white text-slate-950">
                                    <Building2 v-if="item.type === 'Unit'" class="h-5 w-5" />
                                    <Network v-else class="h-5 w-5" />
                                </div>
                                <span class="rounded-full bg-white/10 px-3 py-1 text-[11px] font-bold text-slate-300">
                                    {{ item.type }}
                                </span>
                            </div>
                            <h3 class="mt-6 text-lg font-bold">{{ item.name }}</h3>
                            <p class="mt-2 flex items-center gap-2 text-sm text-slate-400">
                                <Users class="h-4 w-4" />
                                {{ item.members }} anggota
                            </p>
                        </article>
                    </div>
                </div>
            </section>

            <!-- Kegiatan / Berita cabang -->
            <section id="kegiatan" class="relative overflow-hidden bg-slate-50 py-24 sm:py-32">
                <div class="absolute -right-48 top-0 h-[34rem] w-[34rem] rounded-full bg-blue-200/40 blur-3xl blob blob-two" />
                <div class="absolute -left-40 bottom-[-10rem] h-[30rem] w-[30rem] rounded-full bg-violet-200/30 blur-3xl blob blob-three" />

                <div ref="setRevealRef" class="reveal-on-scroll relative mx-auto max-w-7xl px-5 sm:px-6 lg:px-8">
                    <div class="flex flex-col justify-between gap-7 md:flex-row md:items-end">
                        <div class="max-w-3xl">
                            <span class="inline-flex rounded-full border border-slate-200 bg-white px-4 py-2 text-xs font-bold uppercase tracking-[0.2em] text-slate-600">
                                Kegiatan Cabang
                            </span>
                            <h2 class="mt-6 text-4xl font-black tracking-tight text-slate-950 sm:text-5xl">
                                Aktivitas terbaru cabang ini.
                            </h2>
                            <p class="mt-5 text-base leading-8 text-slate-500 sm:text-lg">
                                Dokumentasi kegiatan dan informasi terbaru dari Pengurus Cabang.
                            </p>
                        </div>

                        <Link href="/berita" class="group inline-flex shrink-0 items-center gap-2 text-sm font-bold text-slate-950">
                            Lihat semua berita
                            <ArrowRight class="h-4 w-4 transition-transform group-hover:translate-x-1" />
                        </Link>
                    </div>

                    <div class="mt-14 grid gap-6 md:grid-cols-2 lg:grid-cols-3">
                        <article
                            v-for="item in displayedNews.slice(0, 3)"
                            :key="item.id"
                            class="group overflow-hidden rounded-3xl border border-slate-200 bg-white shadow-sm transition duration-500 hover:-translate-y-1 hover:shadow-xl"
                        >
                            <div class="relative flex h-52 items-center justify-center overflow-hidden bg-slate-950">
                                <img
                                    v-if="item.image"
                                    :src="item.image"
                                    :alt="item.title"
                                    class="absolute inset-0 h-full w-full object-cover transition duration-700 group-hover:scale-105"
                                />
                                <div v-else class="absolute inset-0">
                                    <div class="absolute -left-10 -top-10 h-40 w-40 rounded-full bg-cyan-400/20 blur-3xl" />
                                    <div class="absolute -bottom-20 -right-10 h-52 w-52 rounded-full bg-violet-500/20 blur-3xl" />
                                    <Newspaper class="relative h-12 w-12 text-white/60 mx-auto mt-16" />
                                </div>
                                <div class="absolute inset-x-0 bottom-0 bg-gradient-to-t from-slate-950/80 to-transparent p-5">
                                    <span class="inline-flex rounded-full bg-white/10 px-3 py-1 text-[10px] font-bold uppercase tracking-wider text-white backdrop-blur">
                                        {{ item.category }}
                                    </span>
                                </div>
                            </div>

                            <div class="p-6">
                                <div class="flex items-center gap-2 text-xs text-slate-400">
                                    <CalendarDays class="h-3.5 w-3.5" />
                                    {{ item.date }}
                                </div>
                                <h3 class="mt-4 text-lg font-bold leading-7 text-slate-950 transition group-hover:text-blue-600">
                                    {{ item.title }}
                                </h3>
                                <p class="mt-3 line-clamp-3 text-sm leading-6 text-slate-500">
                                    {{ item.excerpt }}
                                </p>
                                <div class="mt-6 inline-flex items-center gap-2 text-sm font-bold text-slate-950">
                                    Baca selengkapnya
                                    <ChevronRight class="h-4 w-4 transition-transform group-hover:translate-x-1" />
                                </div>
                            </div>
                        </article>
                    </div>
                </div>
            </section>

            <!-- CTA -->
            <section class="relative overflow-hidden bg-white py-24 sm:py-32">
                <div class="absolute left-[-12rem] top-[-8rem] h-[32rem] w-[32rem] rounded-full bg-cyan-100/60 blur-3xl blob blob-one" />
                <div class="absolute right-[-12rem] bottom-[-10rem] h-[34rem] w-[34rem] rounded-full bg-violet-100/60 blur-3xl blob blob-three" />

                <div ref="setRevealRef" class="reveal-on-scroll relative mx-auto max-w-5xl px-5 text-center sm:px-6 lg:px-8">
                    <div class="rounded-[2.5rem] border border-slate-200 bg-slate-950 px-7 py-14 text-white shadow-2xl sm:px-12 sm:py-16">
                        <Landmark class="mx-auto h-9 w-9 text-cyan-300" />
                        <h2 class="mx-auto mt-7 max-w-3xl text-4xl font-black tracking-tight sm:text-5xl">
                            Ingin bergabung dengan {{ org.name }}?
                        </h2>
                        <p class="mx-auto mt-5 max-w-2xl text-base leading-8 text-slate-400">
                            Masuk ke sistem untuk melihat data anggota, ranting, unit, dan kegiatan cabang secara lengkap.
                        </p>
                        <div class="mt-9">
                            <Link
                                href="/login"
                                class="inline-flex items-center gap-2 rounded-full bg-white px-7 py-3.5 text-sm font-bold text-slate-950 transition hover:bg-slate-200"
                            >
                                Masuk ke Sistem
                                <ArrowRight class="h-4 w-4" />
                            </Link>
                        </div>
                    </div>
                </div>
            </section>
        </main>

        <!-- Footer -->
        <footer class="border-t border-slate-200 bg-slate-950 text-white">
            <div class="mx-auto max-w-7xl px-5 py-14 sm:px-6 lg:px-8">
                <div class="grid gap-12 md:grid-cols-[1.4fr_.6fr_.6fr]">
                    <div>
                        <div class="flex items-center gap-3">
                            <div class="flex h-10 w-10 items-center justify-center rounded-xl bg-white text-sm font-black text-slate-950">
                                OM
                            </div>
                            <div>
                                <div class="text-[11px] font-bold tracking-[0.25em]">ORGANIZATION</div>
                                <div class="text-[9px] tracking-[0.2em] text-slate-500">MANAGEMENT SYSTEM</div>
                            </div>
                        </div>
                        <p class="mt-6 max-w-md text-sm leading-7 text-slate-500">
                            {{ org.name }} adalah bagian dari {{ org.parent }}, mengelola anggota, ranting, unit, dan
                            kegiatan di wilayah {{ org.region }}.
                        </p>
                    </div>

                    <div>
                        <h3 class="text-xs font-bold uppercase tracking-[0.2em] text-slate-400">Navigasi</h3>
                        <div class="mt-5 space-y-3">
                            <a href="#profil" class="block text-sm text-slate-500 transition hover:text-white">Profil</a>
                            <a href="#struktur" class="block text-sm text-slate-500 transition hover:text-white">Struktur</a>
                            <a href="#ranting" class="block text-sm text-slate-500 transition hover:text-white">Ranting & Unit</a>
                            <a href="#kegiatan" class="block text-sm text-slate-500 transition hover:text-white">Kegiatan</a>
                        </div>
                    </div>

                    <div>
                        <h3 class="text-xs font-bold uppercase tracking-[0.2em] text-slate-400">Kontak</h3>
                        <div class="mt-5 space-y-3 text-sm text-slate-500">
                            <p class="flex items-start gap-2">
                                <MapPin class="mt-0.5 h-4 w-4 shrink-0" />
                                {{ org.address }}
                            </p>
                            <p class="flex items-center gap-2">
                                <Phone class="h-4 w-4 shrink-0" />
                                {{ org.phone }}
                            </p>
                            <p class="flex items-center gap-2">
                                <Mail class="h-4 w-4 shrink-0" />
                                {{ org.email }}
                            </p>
                        </div>
                    </div>
                </div>

                <div class="mt-14 flex flex-col gap-4 border-t border-white/10 pt-7 text-xs text-slate-600 sm:flex-row sm:items-center sm:justify-between">
                    <p>© {{ new Date().getFullYear() }} Organization Management System. All rights reserved.</p>
                    <div class="flex items-center gap-2">
                        <span class="h-2 w-2 rounded-full bg-emerald-400" />
                        System Operational
                    </div>
                </div>
            </div>
        </footer>

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

.hero-reveal {
    opacity: 0;
    transform: translateY(24px);
    animation: heroReveal 850ms cubic-bezier(0.22, 1, 0.36, 1) forwards;
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
    opacity: 1;
    transform: translateY(0);
    transition:
        opacity 900ms cubic-bezier(0.22, 1, 0.36, 1),
        transform 900ms cubic-bezier(0.22, 1, 0.36, 1);
}

.reveal-on-scroll.reveal-ready {
    opacity: 0;
    transform: translateY(36px);
}

.reveal-on-scroll.reveal-ready.is-visible {
    opacity: 1;
    transform: translateY(0);
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
    .hero-reveal,
    .reveal-on-scroll,
    .blob {
        opacity: 1;
        transform: none;
        animation: none;
        transition: none;
    }
}

@media (max-width: 640px) {
    .hero-reveal {
        animation-duration: 650ms;
    }
    .reveal-on-scroll {
        transform: translateY(22px);
        transition-duration: 650ms;
    }
}
</style>
