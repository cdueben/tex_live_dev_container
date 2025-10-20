# Use the official ubuntu image
FROM ubuntu:latest

# Install additional tools
USER root
RUN apt update -y && \
  apt full-upgrade -y && \
  apt install -y curl perl git

# Create the tl user and group
RUN groupadd -r tl && useradd -r -g tl -m -d /home/tl -s /bin/bash tl

# Copy the tex live profile
COPY texlive.profile /home/tl/texlive.profile
RUN chown tl:tl /home/tl/texlive.profile

# Switch back to tl user
USER tl

# Download and install tex live
RUN cd /home/tl && \
  curl -L -o install-tl-unx.tar.gz https://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz && \
  zcat < install-tl-unx.tar.gz | tar xf - && \
  TEXLIVE_YEAR=$(ls -d install-tl-[0-9]* | head -n 1 | xargs basename | sed 's/install-tl-\([0-9]\{4\}\).*/\1/') && \
  echo "${TEXLIVE_YEAR}" > texlive_year.txt && \
  cd install-tl-2* && \
  echo "TEXDIR /home/tl/texlive/${TEXLIVE_YEAR}" >> /home/tl/texlive.profile && \
  echo "TEXMFLOCAL /home/tl/texlive/texmf-local" >> /home/tl/texlive.profile && \
  echo "TEXMFHOME /home/tl/texmf" >> /home/tl/texlive.profile && \
  echo "TEXMFSYSVAR /home/tl/texlive/${TEXLIVE_YEAR}/texmf-var" >> /home/tl/texlive.profile && \
  echo "TEXMFSYSCONFIG /home/tl/texlive/${TEXLIVE_YEAR}/texmf-config" >> /home/tl/texlive.profile && \
  echo "TEXMFVAR /home/tl/texlive${TEXLIVE_YEAR}/texmf-var" >> /home/tl/texlive.profile && \
  echo "TEXMFCONFIG /home/tl/texlive${TEXLIVE_YEAR}/texmf-config" >> /home/tl/texlive.profile && \
  perl ./install-tl -profile /home/tl/texlive.profile

# Add tex live to path in .bashrc (for interactive terminals)
RUN cd /home/tl && \
  TEXLIVE_YEAR=$(cat texlive_year.txt) && \
  echo "export PATH=\"/home/tl/texlive/${TEXLIVE_YEAR}/bin/x86_64-linux:\$PATH\"" >> .bashrc && \
  echo "export MANPATH=\"/home/tl/texlive/${TEXLIVE_YEAR}/texmf-dist/doc/man:\$MANPATH\"" >> .bashrc && \
  echo "export INFOPATH=\"/home/tl/texlive/${TEXLIVE_YEAR}/texmf-dist/doc/info:\$INFOPATH\"" >> .bashrc

# Install tex packages
RUN cd /home/tl && \
  TEXLIVE_YEAR=$(cat texlive_year.txt) && \
  /home/tl/texlive/${TEXLIVE_YEAR}/bin/x86_64-linux/tlmgr update --self && \
  /home/tl/texlive/${TEXLIVE_YEAR}/bin/x86_64-linux/tlmgr update --all

# Clean up directory
RUN cd /home/tl && \
  rm -r install-tl-2* install-tl-unx.tar.gz texlive_year.txt texlive.profile
