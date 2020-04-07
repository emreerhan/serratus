.PHONY: simulate data bloom-filter bloom-categorize

#################################################################################
# GLOBALS                                                                       #
#################################################################################

cv_pangenome?=../data/seq/cov0/cov0.fa
threads?=32

#################################################################################
# COMMANDS                                                                      #
#################################################################################

## Run ART to simulate reads from the CV pangenome
simulate: coronavirus_sim.log

## Download the CV pangenome from AWS s3
data: $(cv_pangenome)

## Create bloom filter of CV pangenome without Avian genomes
bloom-filter: biobloomtools/cv_minus_avian.bf

## Categorize reads from entire pangenome against CV minus Avian pangenome
bloom-categorize: biobloomtools/categorize_avian/_summary.tsv

#################################################################################
# PROJECT RULES                                                                 #
#################################################################################

$(cv_pangenome):
	mkdir -p $(shell dirname $@)
	aws s3 cp s3://serratus-public/seq/cov0/cov0.fa $@

coronavirus_sim.log data/coronavirus1.fq data/coronavirus2.fq: $(cv_pangenome)
	@echo "Simulating reads with ART Illumina"
	mkdir -p data
	art_illumina \
		--seqSys HS25 \
		--paired \
		--in $< \
		--len 150 \
		--fcov 20 \
		--rndSeed 42 \
		--mflen 500 \
		--sdev 1 \
		--out coronavirus \
		> coronavirus_sim.log
	mv coronavirus*{aln,fq} data;

data/complete_genomes.fa: $(cv_pangenome)
	seqtk seq $< | grep -A1 'complete genome' > data/complete_genomes.fa

data/avian_cv.fa: data/complete_genomes.fa
	grep -A1 'Avian' data/complete_genomes.fa > $@

biobloomtools/cv_minus_avian.bf: data/genomes_wo_avian.fa
	mkdir -p biobloomtools
	biobloommaker -p cv_minus_avian -o biobloomtools/ -t $(threads) $<

biobloomtools/categorize_avian/_summary.tsv biobloomtools/categorize_avian/_cv_minus_avian.fa: biobloomtools/cv_minus_avian.bf data/coronavirus1.fq data/coronavirus2.fq
	mkdir -p biobloomtools/categorize_avian
	biobloomcategorizer \
		--fa \
		-p biobloomtools/categorize_avian/ \
		-f $< \
		-t $(threads) \
		data/coronavirus1.fq data/coronavirus2.fq

data/genomes_wo_avian.fa: data/complete_genomes.fa
	seqtk seq data/complete_genomes.fa | \
		grep -A1 'complete genome' | \
		awk '/Avian/{getline;next} 1' \
		> data/genomes_wo_avian.fa

#################################################################################
# Self Documenting Commands                                                     #
#################################################################################

.DEFAULT_GOAL := help

# Inspired by <http://marmelab.com/blog/2016/02/29/auto-documented-makefile.html>
# sed script explained:
# /^##/:
# 	* save line in hold space
# 	* purge line
# 	* Loop:
# 		* append newline + line to hold space
# 		* go to next line
# 		* if line starts with doc comment, strip comment character off and loop
# 	* remove target prerequisites
# 	* append hold space (+ newline) to line
# 	* replace newline plus comments by `---`
# 	* print line
# Separate expressions are necessary because labels cannot be delimited by
# semicolon; see <http://stackoverflow.com/a/11799865/1968>
.PHONY: help
help:
	@echo "$$(tput bold)Available rules:$$(tput sgr0)"
	@echo
	@sed -n -e "/^## / { \
		h; \
		s/.*//; \
		:doc" \
		-e "H; \
		n; \
		s/^## //; \
		t doc" \
		-e "s/:.*//; \
		G; \
		s/\\n## /---/; \
		s/\\n/ /g; \
		p; \
	}" ${MAKEFILE_LIST} \
	| LC_ALL='C' sort --ignore-case \
	| awk -F '---' \
		-v ncol=$$(tput cols) \
		-v indent=19 \
		-v col_on="$$(tput setaf 6)" \
		-v col_off="$$(tput sgr0)" \
	'{ \
		printf "%s%*s%s ", col_on, -indent, $$1, col_off; \
		n = split($$2, words, " "); \
		line_length = ncol - indent; \
		for (i = 1; i <= n; i++) { \
			line_length -= length(words[i]) + 1; \
			if (line_length <= 0) { \
				line_length = ncol - indent - length(words[i]) - 1; \
				printf "\n%*s ", -indent, " "; \
			} \
			printf "%s ", words[i]; \
		} \
		printf "\n"; \
	}' \
	| more $(shell test $(shell uname) = Darwin && echo '--no-init --raw-control-chars')
