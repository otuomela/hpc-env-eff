# Build HTML slides with slidefactory into ../docs/slides
# Run before `zensical build` or `zensical serve`.

IMAGE = ghcr.io/csc-training/slidefactory:3.5.0
OUT = ../docs/slides

ifneq ($(shell command -v podman),)
SLIDEFACTORY ?= podman run --rm -v "$(CURDIR)/..:$(CURDIR)/..:z" -w "$(CURDIR)" $(IMAGE)
else
SLIDEFACTORY ?= docker run --rm -u $$(id -u):$$(id -g) -v "$(CURDIR)/..:$(CURDIR)/..:z" -w "$(CURDIR)" $(IMAGE)
endif

LECTURES = $(wildcard [0-9]*.md)

html:
	$(SLIDEFACTORY) slides --format html -o $(OUT) $(LECTURES)

clean:
	rm -rf $(OUT)

.PHONY: html clean
