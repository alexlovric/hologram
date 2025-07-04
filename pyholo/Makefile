PYTHONPATH=
SHELL=/bin/bash
VENV=.venv

# Check architecture
UNAME_S := $(shell uname -s)
ifeq ($(UNAME_S), Darwin)
	VENV_BIN=$(VENV)/bin
else
	ifeq ($(OS), Windows_NT)
		VENV_BIN=$(VENV)/Scripts
	else
		VENV_BIN=$(VENV)/bin
	endif
endif

# Common commands
ACTIVATE=source $(VENV_BIN)/activate
PIP=$(VENV_BIN)/pip3

# Ensure all commands in a rule are executed in the same shell
.ONESHELL:

# Create venv
venv:
	@echo "Creating virtual environment..."
	python3 -m venv $(VENV)
	$(ACTIVATE) && $(PIP) install --upgrade pip
	$(ACTIVATE) && $(PIP) install -r requirements.txt

# Compile release binary
build: venv
	@echo "Compiling release binary..."
	$(ACTIVATE) && maturin develop --release
	@if [ $$? -ne 0 ]; then \
		echo "Build failed!"; \
		exit 1; \
	else \
		echo "Build successful!"; \
	fi

# Remove build artifacts
clean:
	@echo "Removing build artifacts..."
	@rm -rf $(VENV) target
	@cargo clean
	@echo "Build artifacts removed successfully!"

# Run python benchmarking
bench:
	@echo "Running pyhon benchmarks..."
	$(ACTIVATE)
	cd benches/
	pytest $(name).py \
	--benchmark-columns='mean, median, stddev, iqr, outliers, rounds, iterations' \
	--benchmark-max-time=10 \
	--benchmark-min-rounds=10 \
	# --benchmark-histogram=$(name) \
	# --benchmark-cprofile='function_name'
	cd ..
	@echo "Benchmarks run successfully!"


# Declare all phony targets
.PHONY: venv build clean
