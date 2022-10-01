# Ubuntuの公式コンテナを軸に環境構築
# 22.04LTS(jammy)ではaptからpython3.8が入っていなかったので20.04LTS(focal)で固定する
# 20.04LTS(focal)は2025年4月までサポートされる
FROM ubuntu:20.04

# python/3.8/slim-bullseye/Dockerfileを参考にした
RUN set -eux; \
	apt-get update; \
	DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
		ca-certificates \
		netbase \
		tzdata \
	; \
	rm -rf /var/lib/apt/lists/*

# 実行環境のインストール
RUN set -eux; \
	apt-get update; \
	DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
		locales less wget curl git \
		gcc-9 g++-9 libboost-dev \
		python3.8 python3-pip pypy3 \
		nodejs npm \
	; \
	rm -rf /var/lib/apt/lists/*

# 一般的なコマンドで使えるように設定
# e.g. python3.8 main.py => python main.py
RUN set -eux; \
	update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 30; \
	update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 30; \
	update-alternatives --install /usr/bin/python python /usr/bin/python3.8 30; \
	update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 30; \
	update-alternatives --install /usr/bin/pypy pypy /usr/bin/pypy3 30; \
	update-alternatives --install /usr/bin/node node /usr/bin/nodejs 30

# AtCoderで使えるPythonライブラリをインストール
# コンテスト補助アプリケーションをインストール(online-judge-tools)
# Pythonでの競技プログラミング用データ構造をインストール
RUN set -eux; \
	pip install --upgrade pip; \
	pip install --no-deps \
		numpy==1.18.2 \
		scipy==1.4.1 \
		joblib==0.14.1 \
		scikit-learn==0.22.2.post1 \
		networkx==2.4 \
		llvmlite==0.31.0 \
		Cython==0.29.16 \
		decorator==4.4.2 \
		numba==0.48.0 \
	; \
	pip install online-judge-tools==11.5.1; \
	pip install git+https://github.com/hinamimi/ac-library-python; \
	pip install git+https://github.com/hinamimi/python-sortedcontainers; \
	rm -r ~/.cache/pip

# コンテスト補助アプリケーションをインストール(atcoder-cli)
# bits/stdc++.h高速化
# C++でAtCoder Library(ACL)を使えるようにする
RUN set -eux; \
	npm install -g atcoder-cli@2.2.0; \
	g++ /usr/include/x86_64-linux-gnu/c++/9/bits/stdc++.h; \
	git clone https://github.com/atcoder/ac-library.git /opt/ac-library
ENV CPLUS_INCLUDE_PATH /opt/ac-library

# ここからは趣味的なもの

# UI関係の設定
RUN localedef -f UTF-8 -i ja_JP ja_JP.UTF-8
ENV LANG="ja_JP.UTF-8" \
	LANGUAGE="ja_JP:ja" \
	LC_ALL="ja_JP.UTF-8" \
	TZ=JST-9 \
	TERM=xterm

# fish関係のインストール
RUN set -eux; \
	apt-get update; \
	DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
		fish \
		fzf \
		inotify-tools \
	; \
	rm -rf /var/lib/apt/lists/*

# 一般ユーザの作成
ARG USERNAME
ARG GROUPNAME
ARG UID
ARG GID
RUN set -eux; \
	groupadd -g $GID $GROUPNAME; \
	useradd -m -s /bin/fish -u $UID -g $GID $USERNAME
USER $USERNAME

# ユーザ設定
WORKDIR /home/$USERNAME/
RUN set -eux; \
	mkdir .vscode problems acc-templates; \
	mkdir -p ~/.config/atcoder-cli-nodejs; \
	mkdir -p ~/.local/share/online-judge-tools; \
	acc config default-test-dirname-format test; \
	acc config default-task-choice all; \
	acc config default-template cpp; \
	ln -s ~/acc-templates/cpp ~/.config/atcoder-cli-nodejs/cpp; \
	ln -s ~/acc-templates/python ~/.config/atcoder-cli-nodejs/python; \
	{ \
		echo "curl -sL https://git.io/fisher | source && fisher install jorgebucaran/fisher"; \
		echo "fisher install 0rax/fish-bd"; \
		echo "fisher install jethrokuan/z"; \
		echo "fisher install jethrokuan/fzf"; \
	} | fish; \
	echo "alias gac='g++ -std=gnu++17 -Wall -Wextra -O2 -DONLINE_JUDGE'" > ~/.config/fish/conf.d/atcoder.fish
