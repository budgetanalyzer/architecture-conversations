# Minimal Claude Code Sandbox
# Ubuntu 24.04 + Node.js + Claude Code CLI
# No Java/Maven - just enough for AI-assisted development

FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

# Install essential packages only
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    git \
    sudo \
    ca-certificates \
    gnupg \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Install Node.js (latest LTS) - required for Claude Code
RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - \
    && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

# Create vscode user with matching UID/GID from host
ARG USERNAME=vscode
ARG USER_UID
ARG USER_GID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m -s /bin/bash $USERNAME \
    && echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Install Claude Code CLI globally
RUN npm install -g @anthropic-ai/claude-code

# Set up working directory with proper ownership
RUN mkdir -p /workspace && chown -R $USER_UID:$USER_GID /workspace

# Create .anthropic directory for credentials
RUN mkdir -p /home/$USERNAME/.anthropic && chown -R $USER_UID:$USER_GID /home/$USERNAME/.anthropic

# Copy entrypoint script
COPY entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh

# Switch to non-root user
USER $USERNAME

WORKDIR /workspace

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
CMD ["/bin/bash"]
