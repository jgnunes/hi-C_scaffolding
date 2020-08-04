# Hi-C Scaffolding
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
    
    O algoritmo de construção do grafo começa ordenando todos os pares de nós por ordem decrescente do peso da aresta, ou seja, aqueles pares de nós com maiores evidências de contato suportado por dados de Hi-C serão os primeiros. Então, os pares de nós vão sendo, um a um, adicionados ao grafo. Aqueles pares de nós cujos um dos membros já tiverem sido adicionados ao grafo não são inclusos. Ao final, são inseridas arestas para conectar os nós inicial e final de cada contig (ex: início do contig X é conectado ao final do contig X).   
    
    <img src="https://user-images.githubusercontent.com/22843614/89228957-f20e2980-d5b6-11ea-9a20-ffb0c8bd871d.png" width="40%">  
    
4. Construção dos scaffolds  
  Os scaffolds serão construídos a partir do grafo obtido na etapa anterior. Em resumo, primeiro procuramos por pares de nós que estejam conectados apenas a um outro nó (vértice de grau 1). Esses pares são pela definição do grafo, extremos desse grafo (veja no entanto que o grafo pode conter vários sub-grupos/scaffolds, caracterizando vários pares de nó extremos). A partir de um desses nós percorremos o caminho até o outro e contamos o número de arestas nesse caminho, incluindo arestas que conectam dois nós de um mesmo contig (representando suas extremidades inicial e final). Se o número de arestas contado for superior a um limite previamente delimitado Nth, então este subconjunto do grafo será marcado como um scaffold semente (*seed scaffold*). Se inferior, será marcado como um scaffold pequeno (*small scaffold*). Finalmente, cada contig de cada scaffold pequeno é inserido em um scaffold semente, na posição e orientação que maximizem o somatório dos pesos das arestas naquele scaffold semente. Uma vez que todos os contigs dos scaffolds pequenos tenham sido alocados em algum scaffold semente, o processo está finalizado.   

### Insigths interessantes  
#### É possível utilizar dados de Chicago Library  
> "Our method can be extended to leverage other chromatin interaction datasets such as Dovetail Chicago libraries [40] and can adapt to their chromosomal contact model."

### Inputs necessários  
O programa SALSA requer três inputs principais: 
* Arquivo de alinhamento no formato BED, o que significa que provavelmente após o mapeamento das leituras Hi-C contra a montagem inicial precisaremos fazer uma conversão do formato BAM (que é o output padrão dos alinhadores populares, Bowtie e BWA-MEM) para o formato BED  
* Arquivo descrevendo os tamanhos dos contigs da montagem inicial  
* Nome da enzima de restrição utilizada para construção da biblioteca Hi-C  
  - No nosso caso, a enzima utilizada foi a DpnII, como documentado no próprio [e-mail da Dovetail](https://mail.google.com/mail/u/0/?tab=cm#starred/FMfcgxwGCkkFBSHKWfVmjGNHwLLKVHhR)

**Importante**: Para mais detalhes de como gerar os dois arquivos, por favor recorrer à [documentação](https://github.com/marbl/SALSA#how-to-run-the-code) do SALSA no github  

### Output: como interpretar?  
Por padrão, o programa salsa armazena todos seus arquivos no diretório *SALSA_OUT_DIR*. O programa gera uma série de arquivos intermediários, mas provavelmente o arquivo no qual estaremos interessado de fato é o arquivo contendo os scaffolds finais: *scaffolds_FINAL.fasta*. Além dele, o arquivo *scaffolds_FINAL.agp* descreve no formato *AGP* a ordem e orientação de cada contig em cada scaffold.  
Por fim, pode ser de nosso interesse converter o arquivo *scaffolds_FINAL.fasta* para o formato *.hic*, o qual pode ser utilizado para se visualizar a matriz de contatos dos loci do genoma usando programas como o Juicebox. Mais detalhes sobre a conversão de *.fasta* para *.hic* podem ser encontrados no próprio [repositório do SALSA no github](https://github.com/marbl/SALSA#generate-hic-file-from-salsa-scaffolds). Eventualmente, vamos querer que a visualização dos scaffolds do nosso genoma final se pareça com essa visualização gerada pelo Juicebox para os contatos entre os cromossomos humanos:  

<img src="https://user-images.githubusercontent.com/22843614/89295465-6be5f780-d637-11ea-9032-9d3e29a3dd0b.png" width="40%">

## Curadoria manual pós-scaffolding 
A curadoria manual é uma etapa de correção de eventuais erros de scaffolding causados por predição errônea de programas de bioinformática como o SALSA. A ideia aqui é que a partir da investigação da matriz de frequência de contatos do Hi-C conseguiremos identificar e corrigir eventuais problemas na montagem.  

O programa Juicebox tem um módulo específico para identificar e corrigir erros de montagem, chamado *Juicebox Assembly Tools*. No artigo de lançamento do módulo, Dudchenko e colaboradores (2018) demonstram como o módulo pode ser utilizado para detecção de translocações, inversões e erros de junção (*misjoins*). De acordo com os autores, esses erros se manifestam em padrões identificáveis (indicados por grandes setas nos painés esquerdos abaixo) nos mapas de frequência de contato obtidos por dados Hi-C:  

a) Erros de junção: quando duas sequências são unidas erroneamente, isso se manifesta sob a forma de um grande clarão (região de muito baixa densidade de contato) no quadrante superior direito e inferior esquerdo.  

<img src="https://user-images.githubusercontent.com/22843614/89344484-5646f100-d67c-11ea-8d39-64c98d1c713e.png" width="80%"> 

b) Translocações: se manifestam na forma de gravatas borboletas (*bowties*) horizontais ou verticais, cujos pontos centrais representam loci que estão fisicamente próximos no genoma, porém posicionados erroneamente distantes na montagem.  

<img src="https://user-images.githubusercontent.com/22843614/89343968-8346d400-d67b-11ea-83f3-1dbf36d37fa5.png" width="80%">

b) Inversões: se manifestam na forma de gravatas borboletas paralelas à diagonal principal. 

<img src="https://user-images.githubusercontent.com/22843614/89344140-cbfe8d00-d67b-11ea-87a4-3da215fea151.png" width="80%">  

Para todos esses três tipos de erros comentados, é possível corrigir as montagens com um simples arrastar de *mouse* no Juicebox, utilizando para isso o módulo *Juicebox Assembly Tools*. As correções aplicadas utilizando esta ferramenta estão ilustradas nos painéis centrais das figuras acima. Finalmente, os painéis da direita ilustram a matriz atualizada após as correções serem aplicadas. 

## Referências  
### SALSA
* [Paper](https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-017-3879-z)  
* [Repositório no Github](https://github.com/marbl/SALSA)  

### Curadoria manual   
[Dudchenko et al., 2018](https://www.biorxiv.org/content/10.1101/254797v1.full#F2)

