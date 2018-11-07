# VERSION           3
FROM base/archlinux:latest
MAINTAINER Tiago de Paula Peixoto <tiago@skewed.de>

RUN echo 'Server=https://archive.archlinux.org/repos/2018/06/28/$repo/os/$arch' > /etc/pacman.d/mirrorlist

RUN pacman-key --refresh-keys
RUN pacman -Suy --noconfirm
RUN pacman -S binutils make gcc fakeroot --noconfirm --needed
RUN pacman -S expac yajl git --noconfirm --needed
RUN pacman -S sudo grep file --noconfirm --needed

RUN pacman -S sudo boost python3 python3-scipy python-numpy \
              cgal cairomm python-cairo sparsehash cairomm   \
	      autoconf-archive pkg-config patch --noconfirm --needed

ENV MAKEPKG_USER=mkpkg \
    MAKEPKG_GROUP=mkpkg \
    MAKEPKG_ROOT=/tmp/build

RUN groupadd "${MAKEPKG_USER}" \
    && useradd -g "${MAKEPKG_GROUP}" "${MAKEPKG_USER}"

RUN mkdir -p ${MAKEPKG_ROOT}; chown mkpkg:mkpkg ${MAKEPKG_ROOT}

WORKDIR ${MAKEPKG_ROOT}

USER ${MAKEPKG_USER}
RUN curl -o PKGBUILD https://aur.archlinux.org/cgit/aur.git/plain/PKGBUILD?h=python-graph-tool
RUN makepkg PKGBUILD --needed CXXFLAGS="-mtune=generic -O3 -pipe -flto=4 -ffunction-sections -fdata-sections" LDFLAGS="-Wl,--gc-sections"

USER root
RUN pacman -U python-graph-tool-*-x86_64.pkg.tar.xz --noconfirm --needed

WORKDIR /root

RUN rm -rf ${MAKEPKG_ROOT}/*

RUN pacman -S ipython gtk3 python-gobject python-matplotlib python-pandas jupyter-notebook mathjax python-cairocffi pandoc --noconfirm --needed
RUN pacman -S r python-pip

RUN pip install nibabel h5py pandas rpy2 networkx statsmodels scikit-learn
RUN useradd -m -g users user
