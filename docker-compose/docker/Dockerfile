FROM python:3.12-slim

WORKDIR /opt/v8std

RUN apt-get update \
    && apt-get install -y --no-install-recommends fonts-dejavu-core \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt /opt/v8std/requirements.txt
COPY requirements-mcp.txt /opt/v8std/requirements-mcp.txt
COPY scripts /opt/v8std/scripts

RUN chmod +x /opt/v8std/scripts/generate_social_cards.py \
    /opt/v8std/scripts/generate_search_vectors.py \
    /opt/v8std/scripts/install_zensical.sh \
    /opt/v8std/scripts/run_v8std_mcp.sh \
    /opt/v8std/scripts/v8std_mcp_server.py \
    /opt/v8std/scripts/zensical_docs.sh \
    && /opt/v8std/scripts/install_zensical.sh \
    && python -m pip install --upgrade -r /opt/v8std/requirements-mcp.txt

# Non-root user for the network-facing v8std-mcp service. The docs build/
# serve entrypoint keeps running as root by default because it must write
# into the bind-mounted repo checkout (../:/docs) regardless of which host
# uid owns it (e.g. CI runners); the mcp service normally only reads /docs
# and writes to a world-writable cache dir, so it drops to this user via
# the `user:` override in docker-compose.yml.
RUN useradd --system --create-home --uid 10001 --shell /usr/sbin/nologin v8std-mcp

WORKDIR /docs

ENTRYPOINT ["/opt/v8std/scripts/zensical_docs.sh"]
CMD ["serve", "--dev-addr=0.0.0.0:8000"]
