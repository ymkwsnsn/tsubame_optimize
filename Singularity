Bootstrap: yum
OSVersion: 7
MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
Include: yum

%help

%setup

%files

%labels

%environment
    export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/lib

%post
    echo "Installing Development Tools YUM group"
    yum -y groupinstall "Development Tools"
    echo "Installing OFED Infiniband Support"
    yum -y install libibverbs-devel
    yum -y install infiniband-diags perftest qperf opensm
    yum -y install numactl-libs hwloc-libs libfabric libibverbs infinipath-psm
    yum -y groupinstall "Infiniband Support"
    echo "Installing OpenMPI into container..."
    yum -y install wget
    OPENMPI_VERSION=2.1.2
    OPENMPI_NAME=openmpi-${OPENMPI_VERSION}
    wget https://download.open-mpi.org/release/open-mpi/v2.1/${OPENMPI_NAME}.tar.gz
    tar -xvf ${OPENMPI_NAME}.tar.gz
    cd ${OPENMPI_NAME}
    ./configure --prefix=/usr/local --with-sge --with-cuda --with-psm2 --enable-orterun-prefix-by-default --enable-mpi-cxx --with-lustre=/usr --with-hwloc --with-io-romio-flags=“--with-file-system=nfs+lustre --enable-f77 --enable-f90"
    make all install
    /usr/local/bin/mpicc examples/ring_c.c -o /usr/bin/mpi_ring
    cd /

#osu bench
    OSU_VERSION=5.5
    OSU_NAME=osu-micro-benchmarks-${OSU_VERSION}
    wget http://mvapich.cse.ohio-state.edu/download/mvapich/${OSU_NAME}.tar.gz
    tar -xvf ${OSU_NAME}.tar.gz
    cd ${OSU_NAME}
    ./configure --prefix=/usr/local CC=/usr/local/bin/mpicc CXX=/usr/local/bin/mpicxx
    make
    make install

%runscript
/usr/local/libexec/osu-micro-benchmarks/mpi/pt2pt/osu_bw
