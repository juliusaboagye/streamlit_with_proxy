FROM python:3.12-slim-bullseye AS app

# Nb. POETRY_HTTP_BASIC_HML_USERNAME is required but not used with token authentication
ENV POETRY_VERSION=1.8\
    POETRY_VIRTUALENVS_IN_PROJECT=true \
    POETRY_NO_INTERACTION=1 \
    POETRY_CACHE_DIR=/opt/.cache \
    POETRY_INSTALLER_MAX_WORKERS=20 \
    PIP_DEFAULT_TIMEOUT=100 

RUN apt-get update --fix-missing \
    && apt-get upgrade -y --fix-missing \
    && apt-get install -y build-essential curl \
    && apt-get clean \
    && apt-get autoclean \
    && apt-get autoremove --purge  -y \
    && pip install "poetry==$POETRY_VERSION"

WORKDIR /app
COPY ./pyproject.toml ./poetry.lock ./

# Install local poetry package, including dependent files & packages
COPY README.md ./
RUN poetry install
COPY ./streamlit_with_proxy ./

# Run Streamlit app
RUN adduser streamlit-user
USER streamlit-user

EXPOSE 8501
HEALTHCHECK --interval=5s --timeout=2s CMD curl --fail http://localhost:8501/locat/_stcore/health

CMD poetry run streamlit run ./app.py


