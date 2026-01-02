FROM python:3.12-slim

# Metadata
LABEL base_image="python:3.12-slim"
LABEL version="1"
LABEL software="relink"
LABEL software.version="1.0.0"
LABEL about.summary="Container for relink pipeline: xiSEARCH, xiFDR, and Python dependencies for crosslinking mass spectrometry analysis"
LABEL about.home="https://github.com/bigbio/relink"
LABEL about.documentation="https://github.com/bigbio/relink"
LABEL about.license="Apache-2.0"
LABEL about.tags="Proteomics,Crosslinking,Mass Spectrometry"
LABEL maintainer="Yasset Perez-Riverol <ypriverol@gmail.com>"

ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1

# Install Java runtime (required for xiSEARCH and xiFDR)
RUN apt-get update && apt-get install -y --no-install-recommends \
    openjdk-21-jre \
    wget \
    unzip \
    build-essential \
    libgomp1 \
    procps \
    && rm -rf /var/lib/apt/lists/*

# Create directory for xiSEARCH
RUN mkdir -p /opt/xisearch

# Download and install xiSEARCH 1.8.11
RUN wget --no-check-certificate \
    https://www.rappsilberlab.org/wp-content/uploads/2025/12/xiSEARCH_1.8.11.zip \
    -O /tmp/xiSEARCH.zip && \
    unzip /tmp/xiSEARCH.zip -d /opt/xisearch && \
    rm /tmp/xiSEARCH.zip && \
    cp /opt/xisearch/xiSEARCH_1.8.11/xiSEARCH.jar /opt/xisearch/xiSEARCH.jar && \
    cp /opt/xisearch/xiSEARCH_1.8.11/xiFDR-2.3.10.jar /opt/xisearch/xiFDR.jar

# Install Python packages for mass recalibration
# Using versions from pycross requirements.txt (pyopenms version left open for pip to resolve)
RUN pip install --no-cache-dir \
    pyopenms \
    polars==1.35.1 \
    polars-runtime-32==1.35.1 \
    matplotlib==3.10.7 \
    seaborn==0.13.2 \
    numpy==2.3.4 \
    pandas==2.3.3 \
    contourpy==1.3.3 \
    cycler==0.12.1 \
    fonttools==4.60.1 \
    kiwisolver==1.4.9 \
    packaging==25.0 \
    pillow==12.0.0 \
    pyparsing==3.2.5 \
    python-dateutil==2.9.0.post0 \
    pytz==2025.2 \
    psutil==7.1.3 \
    pyarrow==22.0.0 \
    six==1.17.0 \
    tzdata==2025.2

# Clean up build tools (keep Java runtime)
RUN apt-get remove -y wget unzip build-essential && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /data/

# Verify installation
RUN java -jar /opt/xisearch/xiSEARCH.jar --help || echo "xiSEARCH installed" && \
    ls -lh /opt/xisearch/*.jar && \
    python -c "import pyopenms; print(f'pyopenms {pyopenms.__version__}')" && \
    python -c "import polars; print(f'polars {polars.__version__}')"
