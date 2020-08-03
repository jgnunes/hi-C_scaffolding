# hi-C_scaffolding
Estudo acerca da etapa de scaffolding com dados de Hi-C, com a finalidade de gerar um genoma platinum

## Aspectos teóricos
O scaffolding baseado em dados de Hi-C toma proveito das informações de interação de cromatina oferecida pelo sequenciamento de bibliotecas Hi-C para agrupar e ordenar contigs em scaffolds maiores. A premissa teórica da técnica se baseia no fato de que loci presentes em um mesmo cromossomo apresentam frequência de interação 3D muito superior a loci presentes em cromossomos diferentes. E que, dentro de um mesmo cromossomo, a frequência de contato entre loci próximos na sequência linear do DNA tende a ser significativamente mais alta do que loci distantes na sequência. Com base nessas observações, programas de bioinformática recebem como input uma lista de contigs e uma matriz de frequência de contato entre os loci desses contigs, e retorna um arquivo final com os scaffolds contendo os contigs devidamente agrupados e ordenados.  

## SALSA: Simple Assembly Scaffolder  
Nossa escolha inicial de programa para realizar o scaffolding baseado em Hi-C é o SALSA (**S**imple **A**ssemb**L**y **S**c**A**ffolder). Ele oferece uma vantagem sobre o competidor LACHESIS: realiza uma etapa prévia de correção da montagem antes de fazer o scaffolding. Isto contribui não só para não passar adiante os erros, como também para melhorar a eficiência da etapa de scaffolding.  

## Referências  
### SALSA
[Paper](https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-017-3879-z)
[Repositório no Github](https://github.com/marbl/SALSA)
  

