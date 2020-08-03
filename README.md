# hi-C_scaffolding
Estudo acerca da etapa de scaffolding com dados de Hi-C, com a finalidade de gerar um genoma platinum

## Aspectos teóricos
O scaffolding baseado em dados de Hi-C toma proveito das informações de interação de cromatina oferecida pelo sequenciamento de bibliotecas Hi-C para agrupar e ordenar contigs em scaffolds maiores. A premissa teórica da técnica se baseia no fato de que loci presentes em um mesmo cromossomo apresentam frequência de interação 3D muito superior a loci presentes em cromossomos diferentes. E que, dentro de um mesmo cromossomo, a frequência de contato entre loci próximos na sequência linear do DNA tende a ser significativamente mais alta do que loci distantes na sequência. Com base nessas observações, programas de bioinformática recebem como input uma lista de contigs e uma matriz de frequência de contato entre os loci desses contigs, e retorna um arquivo final com os scaffolds contendo os contigs devidamente agrupados e ordenados.  

## SALSA: Simple Assembly Scaffolder  
Nossa escolha inicial de programa para realizar o scaffolding baseado em Hi-C é o SALSA (**S**imple **A**ssemb**L**y **S**c**A**ffolder). Ele oferece uma vantagem sobre o competidor LACHESIS: realiza uma etapa prévia de correção da montagem antes de fazer o scaffolding. Isto contribui não só para não passar adiante os erros, como também para melhorar a eficiência da etapa de scaffolding.  

### Etapas  
Segue uma descrição geral das principais etapas para executar o SALSA. Uma descrição mais detalhada é encontrada no próprio repositório do github do SALSA. Clique [aqui](https://github.com/marbl/SALSA#how-to-run-the-code) para ser redirecionado.   
1. Alinhamento das leituras Hi-C  
  O processo começa pelo alinhamento das leituras Hi-C contra os contigs do genoma rascunho inicial. Após o mapeamento, alguns controles de qualidade são aplicados. 
2. Correção de erros na montagem inicial  
  Após o mapeamento das leituras Hi-C, é realizada uma etapa de correção de erros na montagem inicial. Isso é feito buscando-se por regiões com baixa cobertura física a partir do mapeamento das leituras Hi-C. Uma vez identificada uma região cuja cobertura física é significativamente inferior a de sua vizinhança, o contig original é quebrado em dois contigs menores.  

    ![Cobertura física](https://github.com/jgnunes/hi-C_scaffolding/blob/master/images/physical_coverage.png)

3. Construção do grafo e pontuação das conexões  
  Aqui começa a mágica. A primeira etapa para geração dos scaffolds é na verdade a construção de um grafo. Neste grafo, cada nó representa as extremidades (*ends*) final ou inicial de um contig; cada aresta (*edge*) representa uma conexão entre a extremidade de um contig e a extremidade de outro contig; cada aresta recebe um peso (*weight*), baseado nas informações de frequência de contato geradas pelas leituras Hi-C. É importante notar, no entanto, que o peso não é uma simples contagem de quantas leituras de Hi-C conectaram as duas extremidades do par de contigs em questão. Isso seria injusto pois contigs maiores teriam sempre uma tendência de mostrar mais eventos de contato por uma questão de probabilidade. Para normalizar pelo tamanho dos contigs, o peso de cada aresta é dividido pela quantidade de sítios de corte da enzima de restrição usada no experimento nas extremidades consideradas. Desta forma a equação de cálculo do peso da aresta (*W(E)*) é dada por: 

    <img src="https://user-images.githubusercontent.com/22843614/89224455-e4ed3c80-d5ae-11ea-8c6d-71dc5e938f64.png" width="30%">  
    
    onde *C1* e *C2* são o par de contigs em consideração, *links(C1,C2)* é o número de conexões Hi-C presentes dentro da região de comprimento *l* nas extremidades dos contigs, e *RE(C1)* e *RE(C2)* é o número de sítios de corte pela enzima de restrição na região de comprimento *l* nas extremidades de C1 e C2. 

## Referências  
### SALSA
* [Paper](https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-017-3879-z)  
* [Repositório no Github](https://github.com/marbl/SALSA)  
  

