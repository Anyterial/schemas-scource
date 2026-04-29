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

schemas: output/index.html $(SCHEMAS_JSON) $(SCHEMAS_MD) $(SCHEMAS_HTML) $(INDEXES_HTML)

output/%.json: src/%.yaml $(META_SCHEMAS_JSON)
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --remove-null $(RESOLVE_PATHS_ARGS) --basedir "$(BASEDIR)" --baseid "$(BASEID)" $(META_SCHEMAS_ARGS) $(EXT_SCHEMAS_ARGS) --output "$@" "$<"

output/%.md: src/%.yaml
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --remove-null -f md $(RESOLVE_PATHS_ARGS) --basedir "$(BASEDIR)" --baseid "$(BASEID)" --output "$@" "$<"

$(SCHEMAS_HTML): output/%$(SCHEMAS_HTML_EXT): src/%.yaml $(HTML_TEMPLATE_DEPS)
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --remove-null -f html $(RESOLVE_PATHS_ARGS) --basedir "$(BASEDIR)" --baseid "$(BASEID)" $(HTML_TEMPLATE_ARGS) --output "$@" "$<"

output/%/index.html: src/% $(SCHEMAS) $(HTML_TEMPLATE_DEPS)
	mkdir -p "$(dir $@)"
	$(PROCESS_SCHEMAS) --index --basedir "$(BASEDIR)" --baseid "$(BASEID)" $(RESOLVE_PATHS_ARGS) -f html $(HTML_TEMPLATE_ARGS) $(EXT_SCHEMAS_ARGS) --output "$@" "$<"

output/index.html: $(TEMPLATE_DIR)/root_index.html $(HTML_TEMPLATE_DEPS)
	mkdir -p "$(dir $@)"
	python3 -c "from pathlib import Path; t=Path('$(TEMPLATE_DIR)/root_index.html').read_text(); t=t.replace('__ANYTERIAL_HTML_HEADER__', Path('$(ANYTERIAL_HTML_HEADER_FILE)').read_text()); t=t.replace('__ANYTERIAL_HTML_TOP__', Path('$(ANYTERIAL_HTML_TOP_FILE)').read_text()); Path('$@').write_text(t)"

.PHONY: clean clean_schemas

clean: clean_schemas

clean_schemas:
	rm -rf output
