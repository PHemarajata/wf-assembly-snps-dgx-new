# NVIDIA DGX Station A100 Usage Guide

## Profile Overview

The `dgx_a100` profile is optimized for running the wf-assembly-snps pipeline on an NVIDIA DGX Station A100 with:
- 128 CPU cores (using up to 100 cores, leaving 28 for system overhead)
- 512 GB RAM (using up to 400 GB, leaving 112 GB for system overhead)
- Local execution using Docker containers

## Usage

### Basic Usage
```bash
nextflow run bacterial-genomics/wf-assembly-snps \
  -r main \
  -profile dgx_a100 \
  --input /path/to/your/genomes \
  --outdir results \
  --snp_package parsnp
```

### With Reference Genome
```bash
nextflow run bacterial-genomics/wf-assembly-snps \
  -r main \
  -profile dgx_a100 \
  --input /path/to/your/genomes \
  --ref /path/to/reference.fasta \
  --outdir results \
  --snp_package parsnp
```

### With Recombination Analysis
```bash
nextflow run bacterial-genomics/wf-assembly-snps \
  -r main \
  -profile dgx_a100 \
  --input /path/to/your/genomes \
  --outdir results \
  --snp_package parsnp \
  --recombination gubbins
```

## Resource Allocation

The profile includes optimized resource allocation for different process types:

- **process_single**: 1 CPU, 2 GB RAM
- **process_low**: 4 CPUs, 8 GB RAM  
- **process_medium**: 16 CPUs, 32 GB RAM
- **process_high**: 32 CPUs, 64 GB RAM

### Specific Process Optimizations:
- **CORE_GENOME_ALIGNMENT_PARSNP**: Up to 48 CPUs, 128 GB RAM
- **BUILD_PHYLOGENETIC_TREE_PARSNP**: Up to 32 CPUs, 64 GB RAM
- **RECOMBINATION_GUBBINS**: Up to 24 CPUs, 48 GB RAM
- **SNP distance calculations**: Up to 16 CPUs, 32 GB RAM

## Prerequisites

1. **Docker**: Ensure Docker is installed and running
2. **Nextflow**: Version >=22.04.3
3. **Sufficient disk space**: For Docker images and intermediate files
4. **User permissions**: User should be in the docker group

## Performance Tips

1. **SSD Storage**: Use fast SSD storage for the work directory
2. **Docker Cache**: The profile uses `${HOME}/.nextflow/cache` for Docker images
3. **Concurrent Jobs**: Limited to 20 concurrent jobs to prevent system overload
4. **Memory Management**: Automatic retry with increased resources on failure

## Monitoring

Monitor system resources during execution:
```bash
# Monitor CPU and memory usage
htop

# Monitor Docker containers
docker stats

# Monitor disk usage
df -h
```

## Troubleshooting

If you encounter memory issues, you can reduce the maximum resources:
```bash
nextflow run bacterial-genomics/wf-assembly-snps \
  -r main \
  -profile dgx_a100 \
  --max_cpus 80 \
  --max_memory 300.GB \
  --input /path/to/your/genomes \
  --outdir results
```