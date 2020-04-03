.PHONY: simulate

# GLOBALS

cv_pangenome?=../data/cov0.fa

# COMMANDS

simulate: coronavirus_sim.log

# RULES 

../data/cov0.fa:
	aws s3 cp s3://serratus-public/seq/cov0/cov0.fa ../data/cov0.fa

coronavirus_sim.log: $(cv_pangenome)
	@echo "Simulating reads with ART Illumina"
	mkdir -p data
	art_illumina \
		--seqSys HS25 \
		--paired \
		--in $(cv_pangenome) \
		--len 150 \
		--fcov 20 \
		--rndSeed 42 \
		--mflen 500 \
		--sdev 1 \
		--out coronavirus \
		> coronavirus_sim.log
	mv coronavirus*{aln,fq} data;

.DEFAULT_GOAL: help

# Inspired by <http://marmelab.com/blog/2016/02/29/auto-documented-makefile.html>
# sed script explained:
# /^##/:
#   * save line in hold space
#   * purge line
#   * Loop:
#	   * append newline + line to hold space
#	   * go to next line
#	   * if line starts with doc comment, strip comment character off and loop
#   * remove target prerequisites
#   * append hold space (+ newline) to line
#   * replace newline plus comments by `---`
#   * print line
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
