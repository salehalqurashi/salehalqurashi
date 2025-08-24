import React, { useEffect, useMemo, useState } from "react";
import { motion } from "framer-motion";
import { BarChart, Bar, XAxis, YAxis, Tooltip, ResponsiveContainer } from "recharts";
import { Github, Linkedin, Sun as SunIcon, Moon as MoonIcon, Database, BarChart3, FileSpreadsheet, Terminal, Cpu, GitBranch, Layers, Target, BookOpen } from "lucide-react";

// ====== CONFIG ======
const LINKS = {
  github: "https://github.com/salehalqurashi",
  linkedin: "https://www.linkedin.com/in/saleh-al-qurashi-567766207/",
  email: "mailto:salehhanialqurashi@gmail.com",
  phone: "tel:0501660720",
  resume: "/resume.pdf",
};

// ====== THEME ======
function useTheme() {
  const [theme, setTheme] = useState(
    typeof window !== "undefined" && window.localStorage.getItem("theme")
      ? window.localStorage.getItem("theme")
      : (typeof window !== "undefined" && window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light')
  );
  useEffect(() => {
    const root = document.documentElement;
    root.classList.remove('morning','night','dark');
    if (theme === 'dark') {
      root.classList.add('night','dark');
    } else {
      root.classList.add('morning');
    }
    window.localStorage.setItem('theme', theme);
  }, [theme]);
  return { theme, setTheme };
}

const skillList = [
  { name: 'Power BI', icon: <BarChart3 size={16} className="inline mr-1"/> },
  { name: 'SQL', icon: <Database size={16} className="inline mr-1"/> },
  { name: 'DAX', icon: <Target size={16} className="inline mr-1"/> },
  { name: 'Excel', icon: <FileSpreadsheet size={16} className="inline mr-1"/> },
  { name: 'Python (basic)', icon: <Terminal size={16} className="inline mr-1"/> },
  { name: 'ETL', icon: <Cpu size={16} className="inline mr-1"/> },
  { name: 'Modeling', icon: <Layers size={16} className="inline mr-1"/> },
  { name: 'KPIs', icon: <GitBranch size={16} className="inline mr-1"/> },
  { name: 'Storytelling', icon: <BookOpen size={16} className="inline mr-1"/> },
];

export default function Portfolio() {
  const { theme, setTheme } = useTheme();
  const [repos, setRepos] = useState([]);
  const [query, setQuery] = useState("");
  const [lang, setLang] = useState("All");
  const [sort, setSort] = useState("stars");

  // Fetch GitHub repos
  useEffect(() => {
    (async () => {
      try {
        const res = await fetch(
          "https://api.github.com/users/salehalqurashi/repos?per_page=100&sort=updated",
          { headers: { Accept: "application/vnd.github+json" } }
        );
        const data = await res.json();
        setRepos(Array.isArray(data) ? data.filter((r) => !r.fork) : []);
      } catch (e) {
        console.error(e);
      }
    })();
  }, []);

  const languages = useMemo(() => {
    const set = new Set(repos.map((r) => r.language).filter(Boolean));
    return ["All", ...Array.from(set)];
  }, [repos]);

  const filtered = useMemo(() => {
    const q = query.toLowerCase();
    let r = repos.filter((x) => {
      const inQ =
        x.name.toLowerCase().includes(q) ||
        (x.description || "").toLowerCase().includes(q);
      const inLang = lang === "All" || x.language === lang;
      return inQ && inLang;
    });
    r.sort((a, b) => {
      if (sort === "stars") return (b.stargazers_count || 0) - (a.stargazers_count || 0);
      if (sort === "updated") return new Date(b.updated_at) - new Date(a.updated_at);
      return (b.forks || 0) - (a.forks || 0);
    });
    return r.slice(0, 9);
  }, [repos, query, lang, sort]);

  const skills = [
    { name: "Power BI", val: 92 },
    { name: "SQL", val: 88 },
    { name: "DAX", val: 84 },
    { name: "Excel", val: 86 },
    { name: "Python", val: 65 },
    { name: "Modeling", val: 82 },
  ];

  const scrollToSection = (id) => {
    const el = document.getElementById(id);
    if (el) el.scrollIntoView({ behavior: "smooth", block: "start" });
  };

  return (
    <div
      className="font-sans text-slate-900 dark:text-slate-100 min-h-screen transition-colors duration-300"
      style={{ background: 'var(--page-bg, linear-gradient(to bottom, #ffffff, #f8fafc))' }}
    >
      <style>{`
        :root.morning {
          --page-bg: linear-gradient(to bottom, #ffffff, #f1f5f9);
          --hero-bg: linear-gradient(90deg, #3b82f6, #06b6d4);
          --hero-fg: #0f172a;
          --card-bg: #f8fafc;
          --card-text: #111827;
          --tag-bg: #e2e8f0;
          --tag-text: #111827;
          --section-title: #0f172a;
          --body-text: #1e293b;
        }
        :root.night {
          --page-bg: linear-gradient(to bottom, #020617, #0b1220);
          --hero-bg: radial-gradient(1200px 500px at 10% -20%, rgba(99,102,241,0.35), transparent), linear-gradient(90deg, #1e293b, #0b1220);
          --hero-fg: #e2e8f0;
          --card-bg: #1e293b;
          --card-text: #f1f5f9;
          --tag-bg: #334155;
          --tag-text: #e2e8f0;
          --section-title: #f9fafb;
          --body-text: #e2e8f0;
        }
        .hero h1, .hero p { color: var(--hero-fg); }
        .card { background: var(--card-bg); color: var(--card-text); font-weight:600; border-radius:1rem; padding:1rem; }
        .tag { background: var(--tag-bg); color: var(--tag-text); font-weight:500; padding:0.25rem 0.75rem; border-radius:9999px; margin:0.25rem; display:inline-flex; align-items:center; }
        section h2 { color: var(--section-title); }
        section p, section li, section span { color: var(--body-text); }
      `}</style>

      {/* NAVBAR */}
      <header className="sticky top-0 z-30 border-b border-slate-200/70 dark:border-slate-800/80 backdrop-blur bg-white/70 dark:bg-slate-950/50">
        <div className="mx-auto max-w-7xl px-6 py-4 flex items-center justify-between">
          <a href="#top" className="text-lg sm:text-xl font-bold tracking-tight">
            Saleh Al-Qurashi
          </a>
          <nav className="hidden md:flex items-center gap-6 text-sm">
            <button onClick={() => scrollToSection("about")} className="hover:text-indigo-600 dark:hover:text-indigo-400">About</button>
            <button onClick={() => scrollToSection("projects")} className="hover:text-indigo-600 dark:hover:text-indigo-400">Projects</button>
            <button onClick={() => scrollToSection("experience")} className="hover:text-indigo-600 dark:hover:text-indigo-400">Experience</button>
            <button onClick={() => scrollToSection("skills")} className="hover:text-indigo-600 dark:hover:text-indigo-400">Skills</button>
            <button onClick={() => scrollToSection("certs")} className="hover:text-indigo-600 dark:hover:text-indigo-400">Certificates</button>
            <button onClick={() => scrollToSection("contact")} className="hover:text-indigo-600 dark:hover:text-indigo-400">Contact</button>
          </nav>
          <div className="flex items-center gap-2">
            <button
              onClick={() => setTheme(theme === "dark" ? "light" : "dark")}
              className="p-2 rounded-xl hover:bg-slate-100 dark:hover:bg-slate-800"
              aria-label={theme === 'dark' ? 'Switch to morning' : 'Switch to night'}
              title={theme === 'dark' ? 'Morning mode' : 'Night mode'}
            >
              {theme === "dark" ? <SunIcon /> : <MoonIcon />}
            </button>
            <a href={LINKS.linkedin} target="_blank" rel="noreferrer" className="p-2 rounded-xl hover:bg-slate-100 dark:hover:bg-slate-800" aria-label="LinkedIn">
              <Linkedin />
            </a>
            <a href={LINKS.github} target="_blank" rel="noreferrer" className="p-2 rounded-xl hover:bg-slate-100 dark:hover:bg-slate-800" aria-label="GitHub">
              <Github />
            </a>
            <a href={LINKS.resume} className="hidden sm:inline-flex rounded-xl px-4 py-2 bg-indigo-600 text-white hover:bg-indigo-700 shadow">
              Download CV
            </a>
          </div>
        </div>
      </header>

      {/* HERO */}
      <section id="top" className="hero relative overflow-hidden transition-colors duration-300" style={{ background: 'var(--hero-bg, linear-gradient(90deg,#4f46e5,#1e293b))' }}>
        <div className="mx-auto max-w-7xl px-6 py-20 sm:py-28 grid lg:grid-cols-2 gap-12 items-center">
          <div>
            <motion.h1 initial={{ opacity: 0, y: 10 }} animate={{ opacity: 1, y: 0 }} transition={{ duration: 0.6 }} className="text-4xl sm:text-5xl font-extrabold tracking-tight">
              Data & Business Intelligence
            </motion.h1>
            <motion.p initial={{ opacity: 0, y: 10 }} animate={{ opacity: 1, y: 0 }} transition={{ delay: 0.1, duration: 0.6 }} className="mt-4 text-lg">
              I design decision-grade dashboards with <strong>Power BI</strong>, engineer data models with <strong>SQL/DAX</strong>, and automate ETL to turn data into impact.
            </motion.p>
            <div className="mt-6 flex flex-wrap gap-3">
              <button onClick={() => scrollToSection("projects")} className="rounded-xl px-5 py-3 bg-indigo-600 text-white shadow hover:bg-indigo-700">View Projects</button>
              <a href={LINKS.github} target="_blank" rel="noreferrer" className="rounded-xl px-5 py-3 bg-white dark:bg-slate-900 border dark:border-slate-700 shadow-sm hover:bg-slate-50 dark:hover:bg-slate-800">GitHub</a>
              <a href={LINKS.linkedin} target="_blank" rel="noreferrer" className="rounded-xl px-5 py-3 bg-slate-900 dark:bg-white text-white dark:text-slate-900 shadow hover:opacity-90">LinkedIn</a>
            </div>
          </div>
        </div>
      </section>

      {/* ABOUT */}
      <section id="about" className="scroll-mt-24 py-16">
        <div className="mx-auto max-w-7xl px-6 grid lg:grid-cols-2 gap-12 items-start">
          <div>
            <h2 className="text-2xl sm:text-3xl font-semibold tracking-tight">About</h2>
            <p className="mt-4">I'm Saleh Al-Qurashi, a data & BI analyst who bridges business goals with clean data models and clear visuals. I work across requirements, KPIs, modeling, and stakeholder storytelling.</p>
            <div className="mt-6 flex flex-wrap gap-2">
              {skillList.map((s)=> (<span key={s.name} className="tag">{s.icon}{s.name}</span>))}
            </div>
          </div>
        </div>
      </section>

      {/* PROJECTS */}
      <section id="projects" className="scroll-mt-24 py-16">
        <div className="mx-auto max-w-7xl px-6">
          <div className="flex flex-col sm:flex-row sm:items-end sm:justify-between gap-4">
            <div>
              <h2 className="text-2xl sm:text-3xl font-semibold tracking-tight">Projects</h2>
              <p>Open-source work from my GitHub.</p>
            </div>
            <div className="flex flex-wrap gap-2">
              <input value={query} onChange={(e)=>setQuery(e.target.value)} placeholder="Search projects" className="rounded-xl border border-slate-200 dark:border-slate-700 bg-white dark:bg-slate-900/60 px-3 py-2 text-sm"/>
              <select value={lang} onChange={(e)=>setLang(e.target.value)} className="rounded-xl border border-slate-200 dark:border-slate-700 bg-white dark:bg-slate-900/60 px-3 py-2 text-sm">
                {languages.map((l)=> <option key={l}>{l}</option>)}
              </select>
              <select value={sort} onChange={(e)=>setSort(e.target.value)} className="rounded-xl border border-slate-200 dark:border-slate-700 bg-white dark:bg-slate-900/60 px-3 py-2 text-sm">
                <option value="stars">Sort: Stars</option>
                <option value="updated">Sort: Updated</option>
                <option value="forks">Sort: Forks</option>
              </select>
            </div>
          </div>

          <div className="mt-8 grid md:grid-cols-2 lg:grid-cols-3 gap-6">
            {filtered.map((repo) => (
              <a key={repo.id} href={repo.html_url} target="_blank" rel="noreferrer" className="group card border border-slate-200 dark:border-slate-800 shadow-sm hover:shadow-md transition">
                <div className="flex items-center justify-between gap-3">
                  <h3 className="font-semibold group-hover:text-indigo-700 dark:group-hover:text-indigo-400 truncate">{repo.name}</h3>
                  <span className="text-xs whitespace-nowrap">★ {repo.stargazers_count || 0}</span>
                </div>
                <p className="mt-2 text-sm min-h-[2.5rem]">{repo.description || "No description"}</p>
                <div className="mt-4 text-xs">Updated {new Date(repo.updated_at).toLocaleDateString()}</div>
              </a>
            ))}
          </div>

          <div className="mt-6 text-right">
            <a href={LINKS.github} target="_blank" rel="noreferrer" className="text-indigo-700 dark:text-indigo-400 hover:underline">View all on GitHub →</a>
          </div>
        </div>
      </section>

      {/* EXPERIENCE */
      <section id="experience" className="scroll-mt-24 py-16 bg-slate-50 dark:bg-slate-900/30">
        <div className="mx-auto max-w-7xl px-6">
          <h2 className="text-2xl sm:text-3xl font-semibold tracking-tight">Experience</h2>
          <ol className="mt-8 relative border-s-l border-slate-200 dark:border-slate-800 pl-6 space-y-8">
            {[
              {
                role: "Business Analyst — Wakeb",
                date: "Feb 2025 – Present • Riyadh",
                pts: [
                  "Built BI dashboards aligned with business analysis.",
                  "Converted requirements into measurable KPIs.",
                  "Automated processes and enforced data checks.",
                ],
              },
              {
                role: "Data Analyst — Bin‑Shihon Group",
                date: "Aug 2024 – Feb 2025 • Jeddah",
                pts: [
                  "Developed decision‑driving dashboards.",
                  "Streamlined workflows with automation.",
                  "Monitored KPIs & improved data quality.",
                ],
              },
              {
                role: "Data Analyst Intern — General Directorate of Passports",
                date: "Jun 2023 – Aug 2023 • Makkah",
                pts: [
                  "+20% decision accuracy (representative).",
                  "-30% Excel processing time.",
                  "+40% engagement with Power BI dashboards.",
                ],
              },
            ].map((e) => (
              <li
                key={e.role}
                className="bg-white dark:bg-slate-900/60 border border-slate-200 dark:border-slate-800 rounded-2xl p-6 shadow-sm"
              >
                <div className="absolute -start-2.5 mt-2 w-5 h-5 rounded-full bg-indigo-600" />
                <div className="font-semibold">{e.role}</div>
                <div className="text-xs mt-0.5">{e.date}</div>
                <ul className="mt-3 list-disc ps-5 space-y-1">
                  {e.pts.map((p) => (
                    <li key={p}>{p}</li>
                  ))}
                </ul>
              </li>
            ))}
          </ol>
        </div>
      </section>

      {/* SKILLS */}
      <section id="skills" className="scroll-mt-24 py-16">
        <div className="mx-auto max-w-7xl px-6 grid lg:grid-cols-2 gap-10 items-center">
          <div>
            <h2 className="text-2xl sm:text-3xl font-semibold tracking-tight">Skills</h2>
            <p className="mt-2">Snapshot of my core BI stack.</p>
            <div className="mt-6 grid grid-cols-2 sm:grid-cols-3 gap-3">
              {[
                { name: "Power BI", val: 92 },
                { name: "SQL", val: 88 },
                { name: "DAX", val: 84 },
                { name: "Excel", val: 86 },
                { name: "Python", val: 65 },
                { name: "Modeling", val: 82 },
              ].map(({ name, val }) => (
                <div
                  key={name}
                  className="rounded-2xl border border-slate-200 dark:border-slate-800 p-4 bg-white dark:bg-slate-900/60"
                >
                  <div className="flex items-center justify-between text-sm font-medium">
                    <span>{name}</span>
                    <span>{val}%</span>
                  </div>
                  <div className="mt-2 h-2 rounded-full bg-slate-100 dark:bg-slate-800">
                    <div
                      className="h-2 rounded-full bg-indigo-600"
                      style={{ width: `${val}%` }}
                    />
                  </div>
                </div>
              ))}
            </div>
          </div>
          <div className="h-64 w-full">
            <ResponsiveContainer width="100%" height="100%">
              <BarChart
                data={[
                  { name: "Power BI", val: 92 },
                  { name: "SQL", val: 88 },
                  { name: "DAX", val: 84 },
                  { name: "Excel", val: 86 },
                  { name: "Python", val: 65 },
                  { name: "Modeling", val: 82 },
                ]}
                margin={{ top: 10, right: 20, left: 0, bottom: 0 }}
              >
                <XAxis dataKey="name" tick={{ fontSize: 12 }} />
                <YAxis tick={{ fontSize: 12 }} domain={[0, 100]} />
                <Tooltip />
                <Bar dataKey="val" radius={[6, 6, 0, 0]} />
              </BarChart>
            </ResponsiveContainer>
          </div>
        </div>
      </section>

      {/* CERTIFICATES */}
      <section id="certs" className="scroll-mt-24 py-16 bg-slate-50 dark:bg-slate-900/30">
        <div className="mx-auto max-w-7xl px-6">
          <h2 className="text-2xl sm:text-3xl font-semibold tracking-tight">Certificates</h2>
          <ul className="mt-6 grid sm:grid-cols-2 lg:grid-cols-3 gap-4">
            {[
              { t: "IBM SkillsBuild — Data Fundamentals", d: "Issued May 10, 2024" },
              { t: "IBM — Data Analyst Capstone", d: "Issued Apr 28, 2024" },
              { t: "Databases & SQL for Data Science (IBM)", d: "Issued Dec 5, 2023" },
              { t: "MySQL Workbench (Coursera)", d: "Issued Oct 20, 2023" },
            ].map((c) => (
              <li
                key={c.t}
                className="bg-white dark:bg-slate-900/60 border border-slate-200 dark:border-slate-800 rounded-2xl p-4 shadow-sm"
              >
                <div className="font-medium">{c.t}</div>
                <div className="text-sm">{c.d}</div>
              </li>
            ))}
          </ul>
        </div>
      </section>

      {/* CONTACT */}
      <section id="contact" className="scroll-mt-24 py-16">
        <div className="mx-auto max-w-7xl px-6 grid lg:grid-cols-2 gap-10 items-center">
          <div>
            <h2 className="text-2xl sm:text-3xl font-semibold tracking-tight">Let's work together</h2>
            <p className="mt-2">Email, phone, or LinkedIn — I reply fast.</p>
            <div className="mt-6 flex flex-wrap items-center gap-3">
              <a
                href={LINKS.email}
                className="rounded-xl px-5 py-3 bg-indigo-600 text-white hover:bg-indigo-700"
              >
                Email me
              </a>
              <a
                href={LINKS.phone}
                className="rounded-xl px-5 py-3 bg-slate-900 dark:bg-white text-white dark:text-slate-900"
              >
                Call / WhatsApp
              </a>
              <a
                href={LINKS.linkedin}
                target="_blank"
                rel="noreferrer"
                className="rounded-xl px-5 py-3 border border-slate-200 dark:border-slate-700"
              >
                LinkedIn
              </a>
            </div>
          </div>
          <div className="bg-white dark:bg-slate-900/60 border border-slate-200 dark:border-slate-800 rounded-2xl p-6">
            <form action="https://formspree.io/f/your-id" method="POST" className="grid gap-3">
              <input
                name="name"
                placeholder="Your name"
                className="rounded-xl border border-slate-200 dark:border-slate-700 bg-transparent px-3 py-2"
              />
              <input
                name="email"
                type="email"
                placeholder="Email"
                className="rounded-xl border border-slate-200 dark:border-slate-700 bg-transparent px-3 py-2"
              />
              <textarea
                name="message"
                placeholder="Tell me about your project"
                rows={4}
                className="rounded-xl border border-slate-200 dark:border-slate-700 bg-transparent px-3 py-2"
              />
              <button className="rounded-xl px-5 py-3 bg-indigo-600 text-white hover:bg-indigo-700">
                Send
              </button>
            </form>
          </div>
        </div>
      </section>

      {/* FOOTER */}
      <footer className="border-t border-slate-200 dark:border-slate-800">
        <div className="mx-auto max-w-7xl px-6 py-6 text-sm flex flex-col sm:flex-row items-center justify-between gap-3">
          <p>© {new Date().getFullYear()} Saleh Al‑Qurashi. All rights reserved.</p>
          <div className="flex items-center gap-3">
            <a href={LINKS.linkedin} target="_blank" rel="noreferrer" className="p-2 rounded-xl hover:bg-slate-100 dark:hover:bg-slate-800" aria-label="LinkedIn">
              <Linkedin />
            </a>
            <a href={LINKS.github} target="_blank" rel="noreferrer" className="p-2 rounded-xl hover:bg-slate-100 dark:hover:bg-slate-800" aria-label="GitHub">
              <Github />
            </a>
            <a href={LINKS.resume} className="p-2 rounded-xl hover:bg-slate-100 dark:hover:bg-slate-800">Résumé</a>
          </div>
        </div>
      </footer>
    </div>
  );
}
