#---*- Makefile -*-------------------------------------------------------

# Use "make schemas schemas_html_pretty=true" to apply OPTIMADE styling to the html pages

# Use "make schemas schemas_html_ext=true" to generate html files with .html extensions also for files meant to be served
# without extensions, which is useful for hosting, e.g., on github that automatically redirects URLs without extensions.

.PHONY: all
all: schemas

###############################
# Basic configuration options #
###############################

# Path to the process_schemas program
PROCESS_SCHEMAS=dependencies/submodules/optimade-property-tools/bin/process_schemas
# The base of the URI for the generated property definitions
BASEID=https://schemas.anyterial.se/v0.1/
# The versioned directory being processed
BASEDIR=src/v0.1
# The versions of the meta-schemas to use
META_SCHEMAS_VER=v1.3 v1.2

#################################
# Advanced configuation options #
#################################

# Path to the OPTIMADE schemas repo
OPTIMADE_SCHEMAS_DIR=dependencies/submodules/schemas
# Path to the meta-schemas to use
META_SCHEMA_PATHS := $(foreach ver,$(META_SCHEMAS_VER),$(OPTIMADE_SCHEMAS_DIR)/meta/$(ver))
# Add the OPTIMADE schemas repo as a path relative to which resolve $$inherit
RESOLVE_PATHS_ARGS=--resolve-path $(OPTIMADE_SCHEMAS_DIR)


ifeq ($(origin schemas_html_pretty), undefined)
	ANYTERIAL_HTML_HEADER ?=
	ANYTERIAL_HTML_TOP ?=
else
	ANYTERIAL_HTML_HEADER = <link rel="preconnect" href="https://fonts.googleapis.com"><link rel="preconnect" href="https://fonts.gstatic.com" crossorigin><link href="https://fonts.googleapis.com/css2?family=Fraunces:wght@500;600&family=Instrument+Sans:wght@400;500;600&display=swap" rel="stylesheet"><style>:root{--theme-name:"twilight";--paper:\#f7f2e8;--ink:\#112b2e;--ink-soft:\#4f686c;--line:rgba(17,43,46,0.2);--accent:\#cf5a2f;--accent-rgb:207,90,47;--accent-soft:\#f1d9c7;--panel:\#f8f1e6;--surface:\#fffdfa;--surface-soft:\#faf4ea;--table-head:\#f6efe2;--bg-layer-a:\#edd6be;--bg-layer-b:\#d2e6e0;--bg-base-a:\#f7f1e6;--bg-base-b:\#ece2d1;--shadow:0 12px 26px rgba(17,43,46,0.1);}html[data-theme="dark"]{--theme-name:"dark";--paper:\#090a0b;--ink:\#e9edf5;--ink-soft:\#9ba9bc;--line:rgba(230,236,245,0.22);--accent:\#ff9b6d;--accent-rgb:255,155,109;--accent-soft:rgba(255,155,109,0.2);--panel:\#141821;--surface:\#16181c;--surface-soft:\#1a1f28;--table-head:\#16181c;--bg-layer-a:\#212736;--bg-layer-b:\#111724;--bg-base-a:\#06080d;--bg-base-b:\#0d1118;--shadow:0 12px 30px rgba(0,0,0,0.42);}html[data-theme="light"]{--theme-name:"light";--paper:\#ffffff;--ink:\#1c2a3a;--ink-soft:\#5c6b7f;--line:rgba(27,52,84,0.2);--accent:\#b44f28;--accent-rgb:180,79,40;--accent-soft:\#f8ddd2;--panel:\#f9fbff;--surface:\#ffffff;--surface-soft:\#f6f9ff;--table-head:\#f4f6fb;--bg-layer-a:\#e9f0ff;--bg-layer-b:\#e6f5ee;--bg-base-a:\#ffffff;--bg-base-b:\#f2f8ff;--shadow:0 12px 26px rgba(16,28,46,0.08);}html{margin:0;min-height:100vh;background:radial-gradient(circle at 18% 4%, var(--bg-layer-a), transparent 52%),radial-gradient(circle at 88% 86%, var(--bg-layer-b), transparent 50%),linear-gradient(150deg, var(--bg-base-a), var(--bg-base-b) 72%);}body{max-width:1100px;margin:0 auto;padding:24px 24px 56px;font-family:"Instrument Sans","Segoe UI",system-ui,sans-serif;color:var(--ink);line-height:1.55;}h1,h2,h3,h4{font-family:"Fraunces",Georgia,serif;line-height:1.2;color:var(--ink);}h1{font-size:clamp(28px,4vw,42px);margin:0 0 0.6ex;}h2{font-size:clamp(22px,3vw,30px);margin:1.4em 0 0.5ex;border-bottom:1px solid var(--line);padding-bottom:6px;}h3{font-size:clamp(18px,2.2vw,24px);margin:1.2em 0 0.4ex;}a{color:var(--accent);text-decoration:none;}a:hover{text-decoration:underline;}code,pre,kbd,samp{font-family:ui-monospace,SFMono-Regular,Menlo,Consolas,monospace;}code{background:var(--surface);border:1px solid var(--line);border-radius:6px;padding:1px 6px;font-size:0.92em;}pre{background:var(--surface);border:1px solid var(--line);border-radius:12px;padding:14px 16px;overflow-x:auto;box-shadow:var(--shadow);}pre code{background:transparent;border:0;padding:0;}table{border-collapse:collapse;width:100%;margin:12px 0;background:var(--surface);border-radius:12px;overflow:hidden;box-shadow:var(--shadow);}th,td{padding:8px 12px;text-align:left;border-bottom:1px solid var(--line);vertical-align:top;}th{background:var(--table-head);font-family:"Fraunces",Georgia,serif;font-weight:600;}tr:last-child td{border-bottom:0;}ul,ol{padding-left:24px;}hr{border:0;border-top:1px solid var(--line);margin:24px 0;}blockquote{border-left:3px solid var(--accent);margin:12px 0;padding:4px 14px;color:var(--ink-soft);background:var(--surface);border-radius:0 8px 8px 0;}.anyterial-header{display:flex;align-items:center;gap:18px;padding:8px 0 18px;border-bottom:1px solid var(--line);margin:0 0 28px;}.anyterial-brand{display:flex;align-items:center;gap:14px;text-decoration:none;color:inherit;flex:1;}.anyterial-header img{width:clamp(56px,7vw,80px);height:auto;display:block;}.anyterial-header .anyterial-title{font-family:"Fraunces",Georgia,serif;font-size:clamp(20px,2.4vw,26px);font-weight:600;line-height:1.15;}.anyterial-header .anyterial-title small{display:block;font-family:"Instrument Sans",sans-serif;font-size:13px;font-weight:400;color:var(--ink-soft);margin-top:2px;letter-spacing:0.02em;}.theme-switch{display:inline-flex;align-items:center;gap:6px;border:1px solid var(--line);border-radius:999px;padding:4px;background:var(--surface-soft);box-shadow:var(--shadow);}.theme-btn{border:1px solid transparent;border-radius:999px;background:transparent;color:var(--ink-soft);width:38px;height:38px;padding:0;display:inline-grid;place-content:center;cursor:pointer;transition:color 150ms ease, border-color 150ms ease, background 150ms ease;}.theme-btn:hover,.theme-btn:focus-visible{color:var(--ink);border-color:var(--line);outline:none;}.theme-btn.is-active{color:var(--ink);border-color:rgba(var(--accent-rgb),0.58);background:rgba(var(--accent-rgb),0.16);}.theme-icon{width:20px;height:20px;display:block;}</style><script>(function(){try{var t=window.localStorage.getItem("anyterial_theme");if(t!=="dark"&&t!=="twilight"&&t!=="light")t="twilight";document.documentElement.setAttribute("data-theme",t);}catch(e){document.documentElement.setAttribute("data-theme","twilight");}})();</script>
	ANYTERIAL_HTML_TOP = <header class="anyterial-header"><a class="anyterial-brand" href="https://www.anyterial.se/"><img src="https://www.anyterial.se/img/anyterial_logo_color.svg" alt="Anyterial"><div class="anyterial-title">Anyterial<small>OPTIMADE property definitions</small></div></a><div class="theme-switch" role="group" aria-label="Theme"><button type="button" class="theme-btn" data-theme-option="dark" aria-label="Use dark theme" title="Dark mode"><svg class="theme-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M12 5C8.13401 5 5 8.13401 5 12C5 15.866 8.13401 19 12 19C15.171 19 18.1395 17.1814 19 14.2899"/><path d="M19 14C18.8319 14 18.6652 13.9941 18.5 13.9824C12.5 15 9.50001 11.5 12 5"/></svg></button><button type="button" class="theme-btn" data-theme-option="twilight" aria-label="Use twilight theme" title="Twilight mode"><svg class="theme-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M8 15C8 12.7909 9.79086 11 12 11C14.2091 11 16 12.7909 16 15"/><line x1="12" y1="8" x2="12" y2="6"/><line x1="5" y1="15" x2="3" y2="15"/><line x1="21" y1="15" x2="19" y2="15"/><line x1="19.071" y1="8.34317" x2="17.6568" y2="9.75738"/><line x1="5.41421" y1="9" x2="6.82843" y2="10.4142"/></svg></button><button type="button" class="theme-btn" data-theme-option="light" aria-label="Use light theme" title="Light mode"><svg class="theme-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><rect x="8" y="8" width="8" height="8" rx="4"/><line x1="12" y1="5" x2="12" y2="3"/><line x1="5" y1="12" x2="3" y2="12"/><line x1="7.75739" y1="16.6569" x2="6.34317" y2="18.0711"/><line x1="16.728" y1="17.3137" x2="18.1422" y2="18.7279"/><line x1="12" y1="21" x2="12" y2="19"/><line x1="21" y1="12" x2="19" y2="12"/><line x1="19.071" y1="5.34317" x2="17.6568" y2="6.75738"/><line x1="5.41421" y1="6" x2="6.82843" y2="7.41421"/></svg></button></div></header><script>(function(){var K="anyterial_theme",D="twilight",O={dark:1,twilight:1,light:1};var r=document.documentElement;var btns=Array.from(document.querySelectorAll("[data-theme-option]"));function n(v){if(typeof v!=="string")return D;var l=v.trim().toLowerCase();return O[l]?l:D;}function apply(t){var a=n(t);r.setAttribute("data-theme",a);btns.forEach(function(b){var o=n(b.getAttribute("data-theme-option"));b.classList.toggle("is-active",o===a);b.setAttribute("aria-pressed",o===a?"true":"false");});}var s=null;try{s=window.localStorage.getItem(K);}catch(e){}apply(s||r.getAttribute("data-theme")||D);btns.forEach(function(b){b.addEventListener("click",function(){var x=n(b.getAttribute("data-theme-option"));apply(x);try{window.localStorage.setItem(K,x);}catch(e){}});});})();</script>
endif

SCHEMAS := $(wildcard src/*/*/*/*/*/*.yaml src/*/*/*/*/*.yaml src/*/*/*/*.yaml src/*/*/*.yaml src/*/*.yaml)
SCHEMAS_JSON = $(patsubst src/%.yaml,output/%.json,$(SCHEMAS))
SCHEMAS_MD = $(patsubst src/%.yaml,output/%.md,$(SCHEMAS))

ifeq ($(origin schemas_html_ext), undefined)
	SCHEMAS_HTML := $(patsubst src/%.yaml,output/%,$(SCHEMAS))
	SCHEMAS_HTML_EXT =
else
	SCHEMAS_HTML := $(patsubst src/%.yaml,output/%.html,$(SCHEMAS))
	SCHEMAS_HTML_EXT = .html
endif

EXT_SCHEMAS := $(filter-out dependencies/submodules/optimade-property-tools/external/json-schema/LICENSE, $(wildcard dependencies/submodules/optimade-property-tools/external/json-schema/*))
EXT_SCHEMAS_ARGS := $(foreach schema,$(EXT_SCHEMAS),--schema $(schema))

META_SCHEMAS_JSON := $(foreach path,$(META_SCHEMA_PATHS),$(wildcard $(path)/optimade/*.json))
META_SCHEMAS_ARGS := $(foreach schema,$(META_SCHEMAS_JSON),--schema $(schema))

INDEXES := $(wildcard src/*)
INDEXES_HTML := $(patsubst src/%,output/%/index.html,$(INDEXES))

.PHONY: schemas

schemas: $(SCHEMAS_JSON) $(SCHEMAS_MD) $(SCHEMAS_HTML) $(INDEXES_HTML)

output/%.json: src/%.yaml $(META_SCHEMAS_JSON)
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --remove-null $(RESOLVE_PATHS_ARGS) --basedir "$(BASEDIR)" --baseid "$(BASEID)" $(META_SCHEMAS_ARGS) $(EXT_SCHEMAS_ARGS) --output "$@" "$<"

output/%.md: src/%.yaml
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --remove-null -f md $(RESOLVE_PATHS_ARGS) --basedir "$(BASEDIR)" --baseid "$(BASEID)" --output "$@" "$<"

$(SCHEMAS_HTML): output/%$(SCHEMAS_HTML_EXT): src/%.yaml
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --remove-null -f html $(RESOLVE_PATHS_ARGS) --basedir "$(BASEDIR)" --baseid "$(BASEID)" --html-header '$(ANYTERIAL_HTML_HEADER)' --html-top '$(ANYTERIAL_HTML_TOP)' --output "$@" "$<"

output/%/index.html: src/% $(SCHEMAS)
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --index --basedir "$(BASEDIR)" --baseid "$(BASEID)" $(RESOLVE_PATHS_ARGS) -f html --html-header '$(ANYTERIAL_HTML_HEADER)' --html-top '$(ANYTERIAL_HTML_TOP)' $(EXT_SCHEMAS_ARGS) --output "$@" "$<"

.PHONY: clean clean_schemas

clean: clean_schemas

clean_schemas:
	rm -rf output
