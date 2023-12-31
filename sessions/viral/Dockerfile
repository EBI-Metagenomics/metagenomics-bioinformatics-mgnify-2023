# Set environments
FROM ubuntu:latest
ENV PATH="/root/miniconda3/envs/virsorter/bin:/root/miniconda3/bin:/opt/data/scripts:${PATH}"
ENV PERL5LIB="/root/miniconda3/envs/virsorter/lib/site_perl/5.26.2:/root/miniconda3/envs/virsorter/lib/perl5/site_perl/5.22.0:${PERL5LIB}"
# Install general utils
RUN apt-get update
RUN apt-get install -y wget
RUN apt-get install make
RUN apt-get install -y git
RUN apt-get install -y gcc
RUN apt-get install -y software-properties-common
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
RUN add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran35/'
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y r-base
# Install conda
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh && \
    bash miniconda.sh -b -p /opt/conda && \
    rm miniconda.sh \

RUN wget \
    https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && mkdir /root/.conda \
    && bash Miniconda3-latest-Linux-x86_64.sh -b \
    && rm -f Miniconda3-latest-Linux-x86_64.sh
# Install dependencies
RUN conda create --name virsorter -c bioconda metagene_annotator prodigal mcl=14.137 muscle blast perl-bioperl perl-file-which hmmer=3.1b2 perl-parallel-forkmanager perl-list-moreutils diamond=0.9.14
# Install VirSorter
RUN git clone https://github.com/simroux/VirSorter.git \
    && cd VirSorter/Scripts \
    && make clean && make
RUN cp /VirSorter/wrapper_phage_contigs_sorter_iPlant.pl /root/miniconda3/envs/virsorter/bin/
RUN cp -r /VirSorter/Scripts /root/miniconda3/envs/virsorter/bin/
# Install additional python packages
RUN pip install --no-cache-dir xlrd ete3==3.1.1 biopython==1.77 pandas==1.0.5
# Install R packages
RUN echo "r <- getOption('repos'); r['CRAN'] <- 'http://cran.us.r-project.org'; options(repos = r);" > ~/.Rprofile && \
    Rscript -e "install.packages('Rcpp'); install.packages('BiocManager'); BiocManager::install('qvalue'); install.packages('glmnet'); \
                install.packages('optparse'); install.packages('gggenes'); install.packages('purrr')"
# Install VirFinder
RUN git clone https://github.com/jessieren/VirFinder.git && \
    cd VirFinder && git checkout $COMMIT && cd ..
RUN R CMD INSTALL /VirFinder/linux/VirFinder_1.1.tar.gz
# Set default working directory
WORKDIR /opt/data


