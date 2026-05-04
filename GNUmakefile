#---*- Makefile -*-------------------------------------------------------

# Use "make schemas schemas_html_pretty=true" to apply OPTIMADE styling to the html pages

# Use "make schemas schemas_html_ext=true" to generate html files with .html extensions also for files meant to be served
# without extensions, which is useful for hosting, e.g., on github that automatically redirects URLs without extensions.

.PHONY: all

default:
	$(MAKE) schemas_html_pretty=true schemas_html_ext=true all

all: schemas

###############################
# Basic configuration options #
###############################

# Path to the process_schemas program
PROCESS_SCHEMAS=dependencies/submodules/optimade-property-tools/bin/process_schemas
# The base of the URI for the generated property definitions
BASEID=https://schemas.anyterial.se/
# The versioned directory being processed
BASEDIR=src
# The versions of the meta-schemas to use
META_SCHEMAS_VER=v1.3 v1.2

#################################
# Advanced configuation options #
#################################

# Path to the OPTIMADE schemas repo
OPTIMADE_SCHEMAS_DIR=dependencies/submodules/optimade-schemas
# Path to the meta-schemas to use
META_SCHEMA_PATHS := $(foreach ver,$(META_SCHEMAS_VER),$(OPTIMADE_SCHEMAS_DIR)/meta/$(ver))
# Add the OPTIMADE schemas repo as a path relative to which resolve $$inherit
RESOLVE_PATHS_ARGS=--resolve-path $(OPTIMADE_SCHEMAS_DIR)


TEMPLATE_DIR=templates

ifeq ($(origin schemas_html_pretty), undefined)
	ANYTERIAL_HTML_HEADER_FILE = $(TEMPLATE_DIR)/empty.html
	ANYTERIAL_HTML_TOP_FILE = $(TEMPLATE_DIR)/empty.html
else
	ANYTERIAL_HTML_HEADER_FILE = $(TEMPLATE_DIR)/html_header.html
	ANYTERIAL_HTML_TOP_FILE = $(TEMPLATE_DIR)/html_top.html
endif

HTML_TEMPLATE_DEPS = $(ANYTERIAL_HTML_HEADER_FILE) $(ANYTERIAL_HTML_TOP_FILE)
HTML_TEMPLATE_ARGS = --html-header "$$(cat $(ANYTERIAL_HTML_HEADER_FILE))" --html-top "$$(cat $(ANYTERIAL_HTML_TOP_FILE))"

SCHEMAS := $(wildcard src/*/*/*/*/*/*.yaml src/*/*/*/*/*.yaml src/*/*/*/*.yaml src/*/*/*.yaml src/*/*.yaml)
SCHEMAS_JSON = $(patsubst src/%.yaml,output/%.json,$(SCHEMAS))
SCHEMAS_MD = $(patsubst src/%.yaml,output/%.md,$(SCHEMAS))

ALL_SCHEMAS := $(wildcard src/*/*/*/*/*/*/*.yaml src/*/*/*/*/*/*.yaml src/*/*/*/*/*.yaml src/*/*/*/*.yaml src/*/*/*.yaml)

DEFINITIONS := $(filter src/defs/%.yaml, $(ALL_SCHEMAS))
DEFINITIONS_JSON := $(patsubst src/%.yaml,output/%.json,$(DEFINITIONS))
DEFINITIONS_MD := $(patsubst src/%.yaml,output/%.md,$(DEFINITIONS))

OTHER_SCHEMAS := $(filter-out src/meta/%.yaml,$(filter-out src/defs/%,$(ALL_SCHEMAS)))
OTHER_SCHEMAS_JSON := $(patsubst src/%.yaml,output/%.json,$(OTHER_SCHEMAS))
OTHER_SCHEMAS_MD := $(patsubst src/%.yaml,output/%.md,$(OTHER_SCHEMAS))

ifeq ($(origin schemas_html_ext), undefined)
	DEFINITIONS_HTML := $(patsubst src/%.yaml,output/%,$(DEFINITIONS))
	DEFINITIONS_HTML_EXT =
else
	DEFINITIONS_HTML := $(patsubst src/%.yaml,output/%.html,$(DEFINITIONS))
	DEFINITIONS_HTML_EXT = .html
endif

EXT_SCHEMAS := $(filter-out dependencies/submodules/optimade-property-tools/external/json-schema/LICENSE, $(wildcard dependencies/submodules/optimade-property-tools/external/json-schema/*))
EXT_SCHEMAS_ARGS := $(foreach schema,$(EXT_SCHEMAS),--schema $(schema))

META_SCHEMAS_JSON := $(foreach path,$(META_SCHEMA_PATHS),$(wildcard $(path)/optimade/*.json))
META_SCHEMAS_ARGS := $(foreach schema,$(META_SCHEMAS_JSON),--schema $(schema))

DEF_INDEXES_HTML = output/defs/index.html
DEF_INDEXES_MD = output/defs/index.md

.PHONY: schemas submodule-optimade-property-tools

schemas: submodule-optimade-property-tools schemas_build
schemas_build: schemas_defs_json schemas_defs_docs schemas_defs_html schemas_other_json schemas_indexes_docs
schemas_defs_json: $(DEFINITIONS_JSON)
schemas_defs_docs: $(DEFINITIONS_MD)
schemas_defs_html: $(DEFINITIONS_HTML)
schemas_other_json: $(OTHER_SCHEMAS_JSON) schemas_defs_json
schemas_indexes_docs: $(DEF_INDEXES_HTML) $(DEF_INDEXES_MD) output/index.html

$(DEFINITIONS_JSON) $(OTHER_SCHEMAS_JSON): output/%.json: src/%.yaml $(META_SCHEMAS_JSON)
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --remove-null $(RESOLVE_PATHS_ARGS) --basedir "$(BASEDIR)" --baseid "$(BASEID)" $(META_SCHEMAS_ARGS) $(EXT_SCHEMAS_ARGS) --output "$@" "$<"

$(DEFINITIONS_MD): output/%.md: src/%.yaml $(META_SCHEMAS_JSON)
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --remove-null -f md $(RESOLVE_PATHS_ARGS) --basedir "$(BASEDIR)" --baseid "$(BASEID)" --output "$@" "$<"

$(DEFINITIONS_HTML): output/%$(DEFINITIONS_HTML_EXT): src/%.yaml $(META_SCHEMAS_JSON)
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --remove-null -f html $(RESOLVE_PATHS_ARGS) --basedir "$(BASEDIR)" --baseid "$(BASEID)" $(HTML_TEMPLATE_ARGS) --output "$@" "$<"

$(DEF_INDEXES_MD): output/%/index.md: src/% $(META_SCHEMAS_JSON)
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --index --basedir "$(BASEDIR)" --baseid "$(BASEID)" $(RESOLVE_PATHS_ARGS) -f md $(EXT_SCHEMAS_ARGS) --output "$@" "$<"

$(DEF_INDEXES_HTML): output/%/index.html: src/% $(META_SCHEMAS_JSON)
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --index --basedir "$(BASEDIR)" --baseid "$(BASEID)" $(RESOLVE_PATHS_ARGS) -f html $(HTML_TEMPLATE_ARGS) $(EXT_SCHEMAS_ARGS) --output "$@" "$<"

output/index.html: $(TEMPLATE_DIR)/root_index.html $(HTML_TEMPLATE_DEPS)
	mkdir -p "$(dir $@)"
	python3 -c "from pathlib import Path; t=Path('$(TEMPLATE_DIR)/root_index.html').read_text(); t=t.replace('__ANYTERIAL_HTML_HEADER__', Path('$(ANYTERIAL_HTML_HEADER_FILE)').read_text()); t=t.replace('__ANYTERIAL_HTML_TOP__', Path('$(ANYTERIAL_HTML_TOP_FILE)').read_text()); Path('$@').write_text(t)"

.PHONY: clean clean_schemas

clean: clean_schemas

# Retain the output directory itself in case it is a submodule/symlink to a schema repo
clean_schemas:
	rm -rf output/defs output/json-ld output/json-schema output/meta

schemas_check_variables:
	@echo "DEFINITIONS = $(DEFINITIONS)"
	@echo "DEFINITIONS_JSON = $(DEFINITIONS_JSON)"
	@echo "DEFINITIONS_HTML = $(DEFINITIONS_HTML)"
	@echo ""
	@echo "OTHER_SCHEMAS = $(OTHER_SCHEMAS)"
	@echo "OTHER_SCHEMAS_JSON = $(OTHER_SCHEMAS_JSON)"
	@echo ""
	@echo "META_SCHEMAS = $(META_SCHEMAS)"
	@echo "META_SCHEMAS_JSON = $(META_SCHEMAS_JSON)"
	@echo "META_SCHEMAS_ARGS = $(META_SCHEMAS_ARGS)"
	@echo ""
	@echo "OPTIMADE_VERSION = $(OPTIMADE_VERSION)"
	@echo "OPTIMADE_VERSION_SUBST = $(OPTIMADE_VERSION_SUBST)"
	@echo ""
	@echo "INDEXES_HTML = $(INDEXES_HTML)"
