import { useState, useEffect, useRef, useCallback } from "react";

// ─── Built-in word bank ───────────────────────────────────────────────────────
const WORD_BANK = {
  "serendipity": {
    word: "serendipity",
    phonetic: "/ˌserənˈdipɪti/",
    partOfSpeech: "noun",
    definitions: [
      "The occurrence and development of events by chance in a happy or beneficial way.",
      "The faculty or phenomenon of finding valuable or agreeable things not sought for."
    ],
    examples: [
      "A series of happy serendipities led her to her dream job.",
      "It was pure serendipity that they met at the airport."
    ],
    synonyms: ["chance", "luck", "fortune", "coincidence", "fate", "providence"],
    antonyms: ["misfortune", "design", "intent", "plan"],
    origin: "Coined by Horace Walpole (1754), from the Persian fairy tale 'The Three Princes of Serendip'."
  },
  "ephemeral": {
    word: "ephemeral",
    phonetic: "/ɪˈfemərəl/",
    partOfSpeech: "adjective",
    definitions: [
      "Lasting for a very short time.",
      "(chiefly of plants) Having a very short life cycle."
    ],
    examples: [
      "Fashion is ephemeral — what's trendy today may be passé tomorrow.",
      "The ephemeral beauty of cherry blossoms draws millions each spring."
    ],
    synonyms: ["transient", "fleeting", "momentary", "transitory", "brief", "short-lived"],
    antonyms: ["permanent", "lasting", "enduring", "eternal", "perpetual"],
    origin: "From Greek ephemeros, from epi- 'on' + hemera 'day'."
  },
  "melancholy": {
    word: "melancholy",
    phonetic: "/ˈmelənkɒli/",
    partOfSpeech: "noun / adjective",
    definitions: [
      "(noun) A feeling of pensive sadness, typically with no obvious cause.",
      "(adjective) Having a feeling of melancholy; sad and pensive."
    ],
    examples: [
      "There was a deep melancholy in his eyes that no smile could hide.",
      "She felt melancholy watching the autumn leaves fall."
    ],
    synonyms: ["sadness", "sorrow", "gloom", "despondency", "dejection", "wistfulness"],
    antonyms: ["happiness", "joy", "cheerfulness", "elation", "bliss"],
    origin: "From Greek melankholia, from melas 'black' + khole 'bile' (in medieval medicine, excess black bile caused depression)."
  },
  "ubiquitous": {
    word: "ubiquitous",
    phonetic: "/juːˈbɪkwɪtəs/",
    partOfSpeech: "adjective",
    definitions: [
      "Present, appearing, or found everywhere.",
      "Constantly encountered; widespread."
    ],
    examples: [
      "The ubiquitous smartphone has transformed how we communicate.",
      "Coffee shops are ubiquitous in this city."
    ],
    synonyms: ["omnipresent", "everywhere", "pervasive", "universal", "widespread", "prevalent"],
    antonyms: ["rare", "scarce", "uncommon", "absent", "infrequent"],
    origin: "From modern Latin ubiquitas, from Latin ubique 'everywhere'."
  },
  "resilience": {
    word: "resilience",
    phonetic: "/rɪˈzɪliəns/",
    partOfSpeech: "noun",
    definitions: [
      "The capacity to recover quickly from difficulties; toughness.",
      "The ability of a substance or object to spring back into shape; elasticity."
    ],
    examples: [
      "The community showed remarkable resilience after the flood.",
      "Children often display surprising resilience in the face of adversity."
    ],
    synonyms: ["toughness", "strength", "flexibility", "adaptability", "hardiness", "fortitude"],
    antonyms: ["fragility", "weakness", "vulnerability", "brittleness"],
    origin: "From Latin resilire 'to rebound', from re- 'back' + salire 'to jump'."
  },
  "eloquent": {
    word: "eloquent",
    phonetic: "/ˈeləkwənt/",
    partOfSpeech: "adjective",
    definitions: [
      "Fluent or persuasive in speaking or writing.",
      "Clearly expressing or indicating something."
    ],
    examples: [
      "She gave an eloquent speech that moved the entire audience.",
      "His silence was eloquent — no words were needed."
    ],
    synonyms: ["articulate", "expressive", "fluent", "persuasive", "well-spoken", "silver-tongued"],
    antonyms: ["inarticulate", "inexpressive", "tongue-tied", "halting"],
    origin: "From Latin eloquentem, from eloqui 'speak out', from e- 'out' + loqui 'speak'."
  },
  "paradox": {
    word: "paradox",
    phonetic: "/ˈpærədɒks/",
    partOfSpeech: "noun",
    definitions: [
      "A seemingly absurd or self-contradictory statement or proposition that, when investigated, may prove to be well-founded or true.",
      "A person or thing that combines contradictory features or qualities."
    ],
    examples: [
      "The paradox of choice suggests having too many options can lead to unhappiness.",
      "She was a paradox — a shy woman who loved the spotlight."
    ],
    synonyms: ["contradiction", "inconsistency", "anomaly", "puzzle", "enigma", "conundrum"],
    antonyms: ["truism", "certainty", "clarity", "axiom"],
    origin: "From Greek paradoxon, from para- 'distinct from' + doxa 'opinion'."
  },
  "nostalgia": {
    word: "nostalgia",
    phonetic: "/nɒˈstaldʒə/",
    partOfSpeech: "noun",
    definitions: [
      "A sentimental longing or wistful affection for the past, typically for a period or place with happy personal associations.",
      "Something done or presented in order to evoke feelings of the past."
    ],
    examples: [
      "She was filled with nostalgia listening to the songs of her youth.",
      "The film's retro aesthetic plays on our nostalgia for the 1980s."
    ],
    synonyms: ["wistfulness", "longing", "reminiscence", "sentimentality", "homesickness"],
    antonyms: ["anticipation", "forward-thinking", "futurism"],
    origin: "From Greek nostos 'homecoming' + algos 'pain' — coined by Swiss physician Johannes Hofer in 1688."
  },
  "intrinsic": {
    word: "intrinsic",
    phonetic: "/ɪnˈtrɪnzɪk/",
    partOfSpeech: "adjective",
    definitions: [
      "Belonging naturally; essential.",
      "Of or relating to the essential nature of a thing; inherent."
    ],
    examples: [
      "Music has an intrinsic power to move the human spirit.",
      "The painting has intrinsic value regardless of its market price."
    ],
    synonyms: ["inherent", "innate", "fundamental", "essential", "natural", "built-in"],
    antonyms: ["extrinsic", "external", "acquired", "incidental", "outward"],
    origin: "From medieval Latin intrinsecus 'inward', from Latin intra 'within' + secus 'alongside'."
  },
  "equanimity": {
    word: "equanimity",
    phonetic: "/ˌekwəˈnɪmɪti/",
    partOfSpeech: "noun",
    definitions: [
      "Mental calmness and composure, especially in a difficult situation.",
      "Evenness of mind under stress."
    ],
    examples: [
      "She accepted the setback with remarkable equanimity.",
      "The diplomat maintained equanimity throughout the tense negotiations."
    ],
    synonyms: ["composure", "calmness", "serenity", "tranquility", "poise", "self-possession"],
    antonyms: ["anxiety", "agitation", "panic", "distress", "turmoil"],
    origin: "From Latin aequanimitas, from aequus 'equal' + animus 'mind'."
  },
  "luminous": {
    word: "luminous",
    phonetic: "/ˈluːmɪnəs/",
    partOfSpeech: "adjective",
    definitions: [
      "Emitting or reflecting especially steady, suffused, or glowing light.",
      "Very bright or radiant; full of light.",
      "(of a person's eyes or expression) Conveying happiness, love, or other positive feelings."
    ],
    examples: [
      "The moon cast a luminous glow across the still water.",
      "Her luminous smile lit up the entire room."
    ],
    synonyms: ["glowing", "radiant", "bright", "shining", "lustrous", "brilliant"],
    antonyms: ["dark", "dim", "dull", "murky", "opaque"],
    origin: "From Latin luminosus, from lumen 'light'."
  },
  "tenacious": {
    word: "tenacious",
    phonetic: "/tɪˈneɪʃəs/",
    partOfSpeech: "adjective",
    definitions: [
      "Tending to keep a firm hold of something; clinging or adhering closely.",
      "Not readily relinquishing a position, principle, or course of action; determined."
    ],
    examples: [
      "She was tenacious in her pursuit of justice.",
      "The tenacious ivy had covered the entire wall over the years."
    ],
    synonyms: ["persistent", "determined", "resolute", "stubborn", "dogged", "unyielding"],
    antonyms: ["yielding", "weak", "irresolute", "flexible", "wavering"],
    origin: "From Latin tenax, tenac- 'holding fast', from tenere 'to hold'."
  },
  "ambiguous": {
    word: "ambiguous",
    phonetic: "/æmˈbɪɡjuəs/",
    partOfSpeech: "adjective",
    definitions: [
      "Open to more than one interpretation; not having one obvious meaning.",
      "Unclear or inexact because a choice between alternatives has not been made."
    ],
    examples: [
      "The law is ambiguous on this point and needs clarification.",
      "His ambiguous smile left her wondering what he was thinking."
    ],
    synonyms: ["unclear", "vague", "equivocal", "indefinite", "cryptic", "obscure"],
    antonyms: ["clear", "definite", "unambiguous", "explicit", "plain"],
    origin: "From Latin ambiguus 'uncertain', from ambigere 'to waver', from ambi- 'both ways' + agere 'drive'."
  },
  "perspicacious": {
    word: "perspicacious",
    phonetic: "/ˌpɜːspɪˈkeɪʃəs/",
    partOfSpeech: "adjective",
    definitions: [
      "Having a ready insight into things; shrewd.",
      "Of acute mental vision or discernment."
    ],
    examples: [
      "The perspicacious investor saw the opportunity before anyone else.",
      "Her perspicacious analysis impressed the entire committee."
    ],
    synonyms: ["shrewd", "perceptive", "astute", "insightful", "sharp", "discerning"],
    antonyms: ["obtuse", "unperceptive", "shallow", "dull", "imperceptive"],
    origin: "From Latin perspicax 'having a sharp sight', from perspicere 'to look through'."
  },
  "aesthetic": {
    word: "aesthetic",
    phonetic: "/iːsˈθetɪk/",
    partOfSpeech: "noun / adjective",
    definitions: [
      "(noun) A set of principles underlying and guiding the work of a particular artist or artistic movement.",
      "(adjective) Concerned with beauty or the appreciation of beauty.",
      "(adjective) Giving or designed to give pleasure through beauty; of pleasing appearance."
    ],
    examples: [
      "The cafe has a minimalist aesthetic that many find calming.",
      "She has a keen aesthetic sense and a gift for design."
    ],
    synonyms: ["artistic", "tasteful", "elegant", "stylistic", "visual", "ornamental"],
    antonyms: ["unaesthetic", "utilitarian", "ugly", "unartistic"],
    origin: "From Greek aisthētikos, from aisthānesthai 'perceive'."
  }
};

const WOTD_POOL = Object.keys(WORD_BANK);

// ─── Helpers ──────────────────────────────────────────────────────────────────
function getDayWord() {
  const day = Math.floor(Date.now() / 86400000);
  return WORD_BANK[WOTD_POOL[day % WOTD_POOL.length]];
}

function speak(text, rate = 0.9) {
  if (!window.speechSynthesis) return;
  window.speechSynthesis.cancel();
  const utt = new SpeechSynthesisUtterance(text);
  utt.rate = rate;
  utt.pitch = 1;
  window.speechSynthesis.speak(utt);
}

function getPosBadgeColor(pos) {
  if (!pos) return "#6B7280";
  const p = pos.toLowerCase();
  if (p.includes("noun")) return "#3B5249";
  if (p.includes("verb")) return "#6B3A2A";
  if (p.includes("adj")) return "#3A4A6B";
  if (p.includes("adv")) return "#5E3A6B";
  return "#6B7280";
}

// ─── Main component ───────────────────────────────────────────────────────────
export default function Dictionary() {
  const [query, setQuery] = useState("");
  const [result, setResult] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [wotd] = useState(getDayWord);
  const [history, setHistory] = useState([]);
  const [favorites, setFavorites] = useState([]);
  const [tab, setTab] = useState("search"); // "search" | "history" | "favorites"
  const [speaking, setSpeaking] = useState(false);
  const [suggestions, setSuggestions] = useState([]);
  const [dark, setDark] = useState(false);
  const inputRef = useRef(null);

  // ── Suggestions ──────────────────────────────────────────────────────────────
  useEffect(() => {
    const q = query.trim().toLowerCase();
    if (q.length < 2) { setSuggestions([]); return; }
    const matches = Object.keys(WORD_BANK).filter(w => w.startsWith(q) && w !== q).slice(0, 6);
    setSuggestions(matches);
  }, [query]);

  // ── Lookup ────────────────────────────────────────────────────────────────────
  const lookup = useCallback(async (word) => {
    const w = word.trim().toLowerCase();
    if (!w) return;
    setQuery(w);
    setSuggestions([]);
    setError(null);
    setLoading(true);
    setResult(null);
    setTab("search");

    // Check built-in bank first
    if (WORD_BANK[w]) {
      setResult(WORD_BANK[w]);
      setLoading(false);
      addToHistory(w);
      return;
    }

    // Fall back to Claude API
    try {
      const prompt = `You are a precise English dictionary. Provide a thorough dictionary entry for the word "${w}".
Respond ONLY with a valid JSON object (no markdown, no extra text) in this exact schema:
{
  "word": string,
  "phonetic": string (IPA notation),
  "partOfSpeech": string (e.g. "noun", "verb", "adjective"),
  "definitions": [string, ...] (1-3 definitions),
  "examples": [string, ...] (2 example sentences),
  "synonyms": [string, ...] (4-6 synonyms),
  "antonyms": [string, ...] (3-5 antonyms),
  "origin": string (brief etymology)
}
If the word does not exist in English, set "word" to null and all other fields to empty strings/arrays.`;

      const resp = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "claude-sonnet-4-6",
          max_tokens: 1000,
          messages: [{ role: "user", content: prompt }]
        })
      });
      const data = await resp.json();
      const text = data.content?.map(b => b.text || "").join("") || "";
      const clean = text.replace(/```json|```/g, "").trim();
      const parsed = JSON.parse(clean);
      if (!parsed.word) {
        setError(`"${w}" was not found. Check the spelling and try again.`);
      } else {
        setResult(parsed);
        addToHistory(w);
      }
    } catch (e) {
      setError("Could not fetch the definition. Please check your connection and try again.");
    }
    setLoading(false);
  }, []);

  const addToHistory = (w) => {
    setHistory(h => [w, ...h.filter(x => x !== w)].slice(0, 20));
  };

  const toggleFavorite = (word) => {
    setFavorites(f =>
      f.includes(word) ? f.filter(x => x !== word) : [word, ...f]
    );
  };

  const isFav = (word) => favorites.includes(word);

  const handleSpeak = () => {
    if (!result) return;
    const text = `${result.word}. ${result.phonetic}. ${result.partOfSpeech}. ${result.definitions[0]}. Example: ${result.examples?.[0] || ""}`;
    setSpeaking(true);
    speak(text);
    setTimeout(() => setSpeaking(false), text.length * 55);
  };

  const stopSpeak = () => {
    window.speechSynthesis?.cancel();
    setSpeaking(false);
  };

  // ── Theme tokens ──────────────────────────────────────────────────────────────
  const t = dark ? {
    bg: "#111210",
    surface: "#1A1C18",
    card: "#222420",
    border: "#2E3028",
    text: "#E8E4D8",
    muted: "#8A8778",
    accent: "#C4A862",
    accentText: "#111210",
    tagBg: "#2E3028",
    wotdBg: "#1C1E0A",
  } : {
    bg: "#F5F0E8",
    surface: "#FDFAF4",
    card: "#FFFFFF",
    border: "#DDD8CA",
    text: "#1C1A14",
    muted: "#7A7260",
    accent: "#8B6914",
    accentText: "#FDFAF4",
    tagBg: "#EDE8DC",
    wotdBg: "#FEFDF7",
  };

  const styles = {
    root: {
      minHeight: "100vh",
      background: t.bg,
      color: t.text,
      fontFamily: "'Georgia', 'Times New Roman', serif",
      transition: "background 0.3s, color 0.3s",
    },
    header: {
      background: t.surface,
      borderBottom: `1px solid ${t.border}`,
      padding: "16px 20px 0",
    },
    headerTop: {
      display: "flex",
      alignItems: "center",
      justifyContent: "space-between",
      marginBottom: "14px",
    },
    logo: {
      display: "flex",
      flexDirection: "column",
    },
    logoTitle: {
      fontFamily: "'Georgia', serif",
      fontWeight: "700",
      fontSize: "22px",
      letterSpacing: "-0.5px",
      color: t.accent,
      lineHeight: "1",
    },
    logoSub: {
      fontFamily: "'Arial', sans-serif",
      fontSize: "10px",
      letterSpacing: "2px",
      textTransform: "uppercase",
      color: t.muted,
      marginTop: "2px",
    },
    themeBtn: {
      background: "none",
      border: `1px solid ${t.border}`,
      borderRadius: "20px",
      padding: "5px 12px",
      cursor: "pointer",
      fontSize: "13px",
      color: t.muted,
      fontFamily: "'Arial', sans-serif",
    },
    searchRow: {
      display: "flex",
      gap: "8px",
      marginBottom: "0",
      position: "relative",
    },
    input: {
      flex: 1,
      border: `1.5px solid ${t.border}`,
      borderRadius: "6px 6px 0 0",
      padding: "10px 14px",
      fontSize: "16px",
      fontFamily: "'Georgia', serif",
      background: t.card,
      color: t.text,
      outline: "none",
    },
    searchBtn: {
      background: t.accent,
      color: t.accentText,
      border: "none",
      borderRadius: "6px",
      padding: "10px 18px",
      cursor: "pointer",
      fontSize: "14px",
      fontFamily: "'Arial', sans-serif",
      fontWeight: "600",
      letterSpacing: "0.5px",
      whiteSpace: "nowrap",
    },
    tabs: {
      display: "flex",
      gap: "0",
      marginTop: "12px",
      borderBottom: `1px solid ${t.border}`,
    },
    tabBtn: (active) => ({
      background: "none",
      border: "none",
      borderBottom: active ? `2px solid ${t.accent}` : "2px solid transparent",
      padding: "8px 14px",
      cursor: "pointer",
      fontSize: "12px",
      letterSpacing: "1px",
      textTransform: "uppercase",
      fontFamily: "'Arial', sans-serif",
      color: active ? t.accent : t.muted,
      fontWeight: active ? "700" : "400",
      transition: "color 0.2s",
    }),
    body: {
      maxWidth: "720px",
      margin: "0 auto",
      padding: "0 16px 40px",
    },
    suggestList: {
      position: "absolute",
      top: "100%",
      left: 0,
      right: "90px",
      background: t.card,
      border: `1px solid ${t.border}`,
      borderTop: "none",
      zIndex: 100,
      borderRadius: "0 0 6px 6px",
      overflow: "hidden",
      boxShadow: "0 4px 12px rgba(0,0,0,0.1)",
    },
    suggestItem: {
      padding: "9px 14px",
      cursor: "pointer",
      fontSize: "15px",
      fontFamily: "'Georgia', serif",
      borderBottom: `1px solid ${t.border}`,
      transition: "background 0.15s",
    },
    wotdCard: {
      background: t.wotdBg,
      border: `1px solid ${t.border}`,
      borderLeft: `3px solid ${t.accent}`,
      borderRadius: "8px",
      padding: "16px 18px",
      marginTop: "20px",
      cursor: "pointer",
    },
    wotdLabel: {
      fontFamily: "'Arial', sans-serif",
      fontSize: "10px",
      letterSpacing: "2px",
      textTransform: "uppercase",
      color: t.accent,
      fontWeight: "700",
      marginBottom: "8px",
    },
    resultCard: {
      background: t.card,
      border: `1px solid ${t.border}`,
      borderRadius: "8px",
      padding: "24px 22px",
      marginTop: "20px",
    },
    wordHeading: {
      fontFamily: "'Georgia', serif",
      fontSize: "32px",
      fontWeight: "700",
      color: t.text,
      lineHeight: "1.1",
      margin: 0,
    },
    phonetic: {
      fontFamily: "'Arial', sans-serif",
      fontSize: "15px",
      color: t.muted,
      marginTop: "4px",
      letterSpacing: "0.5px",
    },
    posBadge: (pos) => ({
      display: "inline-block",
      background: getPosBadgeColor(pos),
      color: "#fff",
      borderRadius: "3px",
      padding: "2px 8px",
      fontSize: "11px",
      fontFamily: "'Arial', sans-serif",
      letterSpacing: "0.8px",
      textTransform: "lowercase",
      fontStyle: "italic",
      marginTop: "10px",
    }),
    divider: {
      border: "none",
      borderTop: `1px solid ${t.border}`,
      margin: "16px 0",
    },
    sectionLabel: {
      fontFamily: "'Arial', sans-serif",
      fontSize: "10px",
      letterSpacing: "2px",
      textTransform: "uppercase",
      color: t.muted,
      fontWeight: "700",
      marginBottom: "8px",
    },
    defItem: {
      fontSize: "15px",
      lineHeight: "1.65",
      marginBottom: "10px",
      paddingLeft: "16px",
      borderLeft: `2px solid ${t.border}`,
      color: t.text,
    },
    exampleItem: {
      fontSize: "14px",
      lineHeight: "1.6",
      color: t.muted,
      fontStyle: "italic",
      marginBottom: "6px",
    },
    tagRow: {
      display: "flex",
      flexWrap: "wrap",
      gap: "6px",
    },
    tag: {
      background: t.tagBg,
      border: `1px solid ${t.border}`,
      borderRadius: "3px",
      padding: "3px 10px",
      fontSize: "13px",
      fontFamily: "'Georgia', serif",
      color: t.text,
      cursor: "pointer",
      transition: "background 0.15s",
    },
    actionRow: {
      display: "flex",
      gap: "10px",
      marginTop: "18px",
      flexWrap: "wrap",
    },
    actionBtn: (primary) => ({
      display: "flex",
      alignItems: "center",
      gap: "6px",
      background: primary ? t.accent : t.tagBg,
      color: primary ? t.accentText : t.text,
      border: `1px solid ${primary ? t.accent : t.border}`,
      borderRadius: "5px",
      padding: "8px 16px",
      cursor: "pointer",
      fontSize: "13px",
      fontFamily: "'Arial', sans-serif",
      fontWeight: "600",
      letterSpacing: "0.3px",
    }),
    originBox: {
      background: t.tagBg,
      borderRadius: "5px",
      padding: "10px 14px",
      fontSize: "13px",
      color: t.muted,
      fontStyle: "italic",
      lineHeight: "1.55",
    },
    listCard: {
      background: t.card,
      border: `1px solid ${t.border}`,
      borderRadius: "8px",
      marginTop: "20px",
      overflow: "hidden",
    },
    listItem: {
      display: "flex",
      alignItems: "center",
      justifyContent: "space-between",
      padding: "12px 16px",
      borderBottom: `1px solid ${t.border}`,
      cursor: "pointer",
      transition: "background 0.15s",
    },
    emptyState: {
      textAlign: "center",
      padding: "40px 20px",
      color: t.muted,
      fontStyle: "italic",
      fontSize: "15px",
    },
    loadingBox: {
      textAlign: "center",
      padding: "40px 20px",
      color: t.muted,
    },
    errorBox: {
      background: "#FEF2F2",
      border: "1px solid #FCA5A5",
      borderRadius: "6px",
      padding: "14px 16px",
      color: "#B91C1C",
      fontSize: "14px",
      marginTop: "20px",
      fontFamily: "'Arial', sans-serif",
    },
  };

  return (
    <div style={styles.root}>
      {/* Header */}
      <div style={styles.header}>
        <div style={styles.headerTop}>
          <div style={styles.logo}>
            <span style={styles.logoTitle}>Lexicon</span>
            <span style={styles.logoSub}>English Dictionary</span>
          </div>
          <button style={styles.themeBtn} onClick={() => setDark(d => !d)}>
            {dark ? "☀ Light" : "☽ Dark"}
          </button>
        </div>
        <div style={{ position: "relative" }}>
          <div style={styles.searchRow}>
            <input
              ref={inputRef}
              style={styles.input}
              placeholder="Search any English word…"
              value={query}
              onChange={e => setQuery(e.target.value)}
              onKeyDown={e => e.key === "Enter" && lookup(query)}
              spellCheck={false}
              autoComplete="off"
            />
            <button style={styles.searchBtn} onClick={() => lookup(query)}>
              Look up
            </button>
          </div>
          {suggestions.length > 0 && (
            <div style={styles.suggestList}>
              {suggestions.map(s => (
                <div
                  key={s}
                  style={styles.suggestItem}
                  onClick={() => lookup(s)}
                  onMouseEnter={e => e.currentTarget.style.background = t.tagBg}
                  onMouseLeave={e => e.currentTarget.style.background = ""}
                >
                  {s}
                </div>
              ))}
            </div>
          )}
        </div>
        <div style={styles.tabs}>
          {["search", "history", "favorites"].map(t2 => (
            <button key={t2} style={styles.tabBtn(tab === t2)} onClick={() => setTab(t2)}>
              {t2 === "history" ? `History (${history.length})` :
               t2 === "favorites" ? `Saved (${favorites.length})` : "Search"}
            </button>
          ))}
        </div>
      </div>

      {/* Body */}
      <div style={styles.body}>
        {/* ── Search tab ── */}
        {tab === "search" && (
          <>
            {/* Word of the Day */}
            {!result && !loading && !error && (
              <div style={styles.wotdCard} onClick={() => lookup(wotd.word)}>
                <div style={styles.wotdLabel}>✦ Word of the Day</div>
                <div style={{ fontSize: "26px", fontWeight: "700", color: t.accent }}>{wotd.word}</div>
                <div style={{ ...styles.phonetic, marginTop: "3px" }}>{wotd.phonetic}</div>
                <div style={{ fontSize: "14px", marginTop: "8px", color: t.muted, fontStyle: "italic" }}>
                  {wotd.definitions[0]}
                </div>
                <div style={{ marginTop: "10px", fontSize: "12px", color: t.accent, fontFamily: "'Arial', sans-serif" }}>
                  Tap to explore →
                </div>
              </div>
            )}

            {/* Loading */}
            {loading && (
              <div style={styles.loadingBox}>
                <div style={{ fontSize: "28px", marginBottom: "10px" }}>⟳</div>
                <div style={{ fontFamily: "'Arial', sans-serif", fontSize: "14px" }}>
                  Looking up <em>{query}</em>…
                </div>
              </div>
            )}

            {/* Error */}
            {error && <div style={styles.errorBox}>⚠ {error}</div>}

            {/* Result */}
            {result && !loading && (
              <div style={styles.resultCard}>
                {/* Word header */}
                <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start" }}>
                  <div>
                    <h1 style={styles.wordHeading}>{result.word}</h1>
                    <div style={styles.phonetic}>{result.phonetic}</div>
                    {result.partOfSpeech && (
                      <span style={styles.posBadge(result.partOfSpeech)}>{result.partOfSpeech}</span>
                    )}
                  </div>
                  <button
                    style={{ background: "none", border: "none", cursor: "pointer", fontSize: "22px", padding: "4px" }}
                    onClick={() => toggleFavorite(result.word)}
                    title={isFav(result.word) ? "Remove from saved" : "Save word"}
                  >
                    {isFav(result.word) ? "★" : "☆"}
                  </button>
                </div>

                <hr style={styles.divider} />

                {/* Definitions */}
                <div style={styles.sectionLabel}>Definitions</div>
                {result.definitions.map((d, i) => (
                  <div key={i} style={styles.defItem}>{i + 1}. {d}</div>
                ))}

                {/* Examples */}
                {result.examples?.length > 0 && (
                  <>
                    <hr style={styles.divider} />
                    <div style={styles.sectionLabel}>In a sentence</div>
                    {result.examples.map((ex, i) => (
                      <div key={i} style={styles.exampleItem}>"{ex}"</div>
                    ))}
                  </>
                )}

                {/* Synonyms */}
                {result.synonyms?.length > 0 && (
                  <>
                    <hr style={styles.divider} />
                    <div style={styles.sectionLabel}>Synonyms</div>
                    <div style={styles.tagRow}>
                      {result.synonyms.map(s => (
                        <span
                          key={s}
                          style={styles.tag}
                          onClick={() => lookup(s)}
                          title={`Look up "${s}"`}
                        >
                          {s}
                        </span>
                      ))}
                    </div>
                  </>
                )}

                {/* Antonyms */}
                {result.antonyms?.length > 0 && (
                  <>
                    <hr style={styles.divider} />
                    <div style={styles.sectionLabel}>Antonyms</div>
                    <div style={styles.tagRow}>
                      {result.antonyms.map(a => (
                        <span
                          key={a}
                          style={styles.tag}
                          onClick={() => lookup(a)}
                          title={`Look up "${a}"`}
                        >
                          {a}
                        </span>
                      ))}
                    </div>
                  </>
                )}

                {/* Origin */}
                {result.origin && (
                  <>
                    <hr style={styles.divider} />
                    <div style={styles.sectionLabel}>Word origin</div>
                    <div style={styles.originBox}>{result.origin}</div>
                  </>
                )}

                {/* Action buttons */}
                <div style={styles.actionRow}>
                  <button
                    style={styles.actionBtn(true)}
                    onClick={speaking ? stopSpeak : handleSpeak}
                  >
                    {speaking ? "⏹ Stop" : "▶ Read aloud"}
                  </button>
                  <button
                    style={styles.actionBtn(false)}
                    onClick={() => speak(result.word, 0.7)}
                  >
                    🔊 Pronounce
                  </button>
                  <button
                    style={styles.actionBtn(false)}
                    onClick={() => {
                      const text = `${result.word}\n${result.phonetic}\n${result.partOfSpeech}\n\n${result.definitions.join("\n")}`;
                      navigator.clipboard?.writeText(text);
                    }}
                  >
                    ⎘ Copy
                  </button>
                </div>
              </div>
            )}
          </>
        )}

        {/* ── History tab ── */}
        {tab === "history" && (
          <div style={styles.listCard}>
            {history.length === 0 ? (
              <div style={styles.emptyState}>No search history yet.<br />Look up a word to get started.</div>
            ) : (
              history.map((w, i) => (
                <div
                  key={w}
                  style={{
                    ...styles.listItem,
                    borderBottom: i < history.length - 1 ? `1px solid ${t.border}` : "none",
                  }}
                  onClick={() => { lookup(w); setTab("search"); }}
                  onMouseEnter={e => e.currentTarget.style.background = t.tagBg}
                  onMouseLeave={e => e.currentTarget.style.background = ""}
                >
                  <span style={{ fontFamily: "'Georgia', serif", fontSize: "16px" }}>{w}</span>
                  <span style={{ color: t.muted, fontSize: "13px" }}>→</span>
                </div>
              ))
            )}
          </div>
        )}

        {/* ── Favorites tab ── */}
        {tab === "favorites" && (
          <div style={styles.listCard}>
            {favorites.length === 0 ? (
              <div style={styles.emptyState}>No saved words yet.<br />Tap ☆ on any word to save it here.</div>
            ) : (
              favorites.map((w, i) => (
                <div
                  key={w}
                  style={{
                    ...styles.listItem,
                    borderBottom: i < favorites.length - 1 ? `1px solid ${t.border}` : "none",
                  }}
                  onMouseEnter={e => e.currentTarget.style.background = t.tagBg}
                  onMouseLeave={e => e.currentTarget.style.background = ""}
                >
                  <span
                    style={{ fontFamily: "'Georgia', serif", fontSize: "16px", cursor: "pointer", flex: 1 }}
                    onClick={() => { lookup(w); setTab("search"); }}
                  >
                    {w}
                  </span>
                  <button
                    style={{ background: "none", border: "none", cursor: "pointer", fontSize: "18px", color: t.accent }}
                    onClick={() => toggleFavorite(w)}
                    title="Remove"
                  >
                    ★
                  </button>
                </div>
              ))
            )}
          </div>
        )}
      </div>
    </div>
  );
}
