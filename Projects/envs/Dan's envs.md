### Baqlava

Note: Use micromamba v1.4.3

[baqlava_env.yaml]
```yaml
`baqlava_env.yaml`
name: baqlava_env
channels:
  - conda-forge
  - bioconda
  - biobakery
dependencies:
  - python=3.12
  - humann=3.9
  - anadama2
  - pip
  - pip:
      - baqlava
```

### Waafle

[waafle_env.yaml]
```yaml
`waafle_env.yaml`
name: waafle_env
channels:
  - conda-forge
  - bioconda
  - biobakery
dependencies:
  - waafle
  - megahit
```


### MSA

[msa_env.yaml]
```yaml
name: msa_env
channels:
  - conda-forge
  - bioconda
  - biobakery
dependencies:
  - clustalo
  - seqkit
  - pal2nal
```
