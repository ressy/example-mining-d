# MINING-D Examples

Trying out [MINING-D] ([paper](https://doi.org/10.1371/journal.pcbi.1007837))

## Setup

With conda:

    conda env update --file environment.yml
    conda activate example-mining-d

The networkx function `connected_component_subgraphs` is used by the script,
but was deprecated in version 2.1 and then removed in version 2.4:

<https://github.com/networkx/networkx/search?q=connected_component_subgraphs>

...so I pinned that at <2.4 in the conda env.  The script also requires [click]
so I added that as well.

## Trying it out

First off, do we get the same output they have for the same input with default
options?

    $ python MINING-D/MINING_D.py -i MINING-D/Data/a_cdr3_file.fasta -o output.fasta
    $ diff -s -q output.fasta MINING-D/Data/output_d_genes.fasta
    Files output.fasta and MINING-D/Data/output_d_genes.fasta differ

Nope.  For one thing the output order and naming isn't guaranteed across
different runs.  Comparing just sequences shows several more in theirs:

    GGGTATAGCAGCAGCTGGTAC
    GGTATAGTGGGAGCTACTAC
    GTGGATATAGTGGCTACGATTAC
    TGACTACAGTAACTAC

The IPython notebook shows why.  Settings:

    k=10
    k_mers = 600
    n_cores = 50
    bidir_alpha = 0.5
    cliq_th = 2
    p_val_th = 4.5*10**(-36)

Default kmers are increased from 300 to 600 and more cores are used and
otherwise it's defaults.

Trying that:

    $ python MINING-D/MINING_D.py -n 600 -i MINING-D/Data/a_cdr3_file.fasta -o output.600.fasta

This version matches.

Information from the Supplemental Notes about some of the parameters:

> The most important parameter of the MINING-D algorithm is m, the number of
> seed k-mers. The default value of m should be different across species, since
> different numbers of D genes take part in the recombination process in each
> species. To decide on the default m for each species, we applied MINING-D to
> all datasets with different values of m. The results are shown in Table A.
> **Based on the results in the table, we chose the following as the default
> values: human (m = 600), mouse (m= 300), rat (m = 300), rhesus macaque (m =
> 600), Bactrian camel (m = 300), and rabbit (m = 100).**
>
> The p-value threshold was chosen to be 10e-36. This value achieves 80% power
> from the test with a sample size of 2000 when the effect size (deviation from
> uniform distribution) is medium, according to the definition of the medium
> effect for chi-squared test. Having a strict (very low) threshold on the
> p-value may lead to some missing nucleotides on the sides of the genes, but
> since we are also doing genomic validation, the whole gene can be recovered
> from the genomic reads. On the other hand, high p-value threshold will not
> only lead to extra nucleotides on the sides, it will also cause more
> extensions to be made from a single k-mer, leading to more false positives.
> As another test, we also tried to extend the known human IMGT genes in
> Healthy Human CDR3 datasets using this threshold. 95% of the time, no
> extension was made to any gene.

## Questions

Why are candidates with motif `TACTACTAC` filtered out?  I see some known D
genes that have very nearly that motif already (for example IMGT's rhesus
`IGHD2-15*01` has TAGTACTAC and `IGHD3-16*01` has TATTACTAC).

[MINING-D]: https://github.com/vinnub/MINING-D
[click]: https://palletsprojects.com/p/click/
