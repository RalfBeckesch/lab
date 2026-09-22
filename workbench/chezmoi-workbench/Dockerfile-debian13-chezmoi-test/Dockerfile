FROM debian:13-slim

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        git \
        neovim \
        openssh-server \
        screen \
        sudo \
        tmux \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir -p /run/sshd

# Test users for applying and validating separate chezmoi configurations.
# Password authentication is intentionally enabled for local test use only.
RUN for user in test1 test2 test3 test4 test5; do \
        useradd --create-home --shell /bin/bash "${user}"; \
        echo "${user}:test" | chpasswd; \
        usermod -aG sudo "${user}"; \
    done \
    && echo '%sudo ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/90-test-users \
    && chmod 0440 /etc/sudoers.d/90-test-users

RUN sed -ri \
        -e 's/^#?PasswordAuthentication .*/PasswordAuthentication yes/' \
        -e 's/^#?PermitRootLogin .*/PermitRootLogin no/' \
        -e 's/^#?UsePAM .*/UsePAM yes/' \
        /etc/ssh/sshd_config

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D", "-e"]
