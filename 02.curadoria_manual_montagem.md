# Curadoria manual 
A curadoria manual é uma etapa de correção de eventuais erros de montagem causados por predição errônea de programas de bioinformática. A ideia aqui é que utilizando dados disponíveis para a espécie conseguiremos identificar e corrigir eventuais incoerências na montagem. A princípio tomaremos como ponto de partida a pipeline de curadoria do time do Instituto Sanger, descrita em maior detalhe [neste artigo](https://doi.org/10.1101/2020.08.12.247734). De modo geral, a estratégia do time do Sanger busca por incoerências na montagem via i) mapas de Hi-C e ii) plot simultâneo de múltiplos dados disponíveis para espécie via navegador *gEVAL*  

## 1. Via mapas de Hi-C  
### 1.1 Utilizando o programa Juicebox

O programa [Juicebox](https://github.com/aidenlab/Juicebox) tem um módulo específico para identificar e corrigir erros de montagem, chamado [*Juicebox Assembly Tools*](https://github.com/aidenlab/Juicebox/wiki/Juicebox-Assembly-Tools). No artigo de lançamento do módulo, Dudchenko e colaboradores (2018) demonstram como o módulo pode ser utilizado para detecção de translocações, inversões e erros de junção (*misjoins*). De acordo com os autores, esses erros se manifestam em padrões identificáveis (indicados por grandes setas nos painés esquerdos abaixo) nos mapas de frequência de contato obtidos por dados Hi-C:  

a) Erros de junção: quando duas sequências são unidas erroneamente, isso se manifesta sob a forma de um grande clarão (região de muito baixa densidade de contato) no quadrante superior direito e inferior esquerdo.  

<img src="https://user-images.githubusercontent.com/22843614/89344484-5646f100-d67c-11ea-8d39-64c98d1c713e.png" width="80%"> 

b) Translocações: se manifestam na forma de gravatas borboletas (*bowties*) horizontais ou verticais, cujos pontos centrais representam loci que estão fisicamente próximos no genoma, porém posicionados erroneamente distantes na montagem.  

<img src="https://user-images.githubusercontent.com/22843614/89343968-8346d400-d67b-11ea-83f3-1dbf36d37fa5.png" width="80%">

c) Inversões: se manifestam na forma de gravatas borboletas paralelas à diagonal principal. 

<img src="https://user-images.githubusercontent.com/22843614/89344140-cbfe8d00-d67b-11ea-87a4-3da215fea151.png" width="80%">  

Para todos esses três tipos de erros comentados, é possível corrigir as montagens com um simples arrastar de *mouse* no Juicebox, utilizando para isso o módulo *Juicebox Assembly Tools*. As correções aplicadas utilizando esta ferramenta estão ilustradas nos painéis centrais das figuras acima. Finalmente, os painéis da direita ilustram a matriz atualizada após as correções serem aplicadas. Caso prefira, pode existe um [vídeo tutorial](https://www.youtube.com/watch?v=Nj7RhQZHM18) do Juicebox Assembly Tools  

### 1.2 Utilizando o programa PretextView  
O time de curadoria do Instituto Sanger parece utilizar o programa [PretextView](https://github.com/wtsi-hpag/PretextView) ao invés do Juicebox. No entanto, pela minha pesquisa inicial o Juicebox parece ser uma ferramenta bem mais estabelecida, possuindo uma documentação bem mais completa e artigo já publicado. Isso me faz perguntar por qual motivo o pessoal do Sanger utiliza o PretextView. Uma possibilidade é porque esse programa foi desenvolvido pelo próprio pessoal do Sanger. Vou tentar ver se consigo alguma informação deste tipo junto à Marcela ou diretamente com algum membro do time de curadoria (um dos autores do [pré-print](https://www.biorxiv.org/content/10.1101/2020.08.12.247734v1.full.pdf) da estratégia de curadoria manual do Sanger) 

## 2. Via plot simultâneo de múltiplos conjuntos de dados contra o genoma  
Em adição ao mapa de Hi-C, o time de curadoria do Instituto Sanger utiliza uma série de outros dados para realizar a curadoria manual da montagem ([Howe et al., 2020](https://doi.org/10.1101/2020.08.12.247734)). A ideia é plotar todos esses dados contra a montagem à procura de eventuais inconsistências entre os dados e a montagem. Essas regiões de inconsistência representam potenciais erros de montagem e, quando possível, devem ser corrigidas (em certos casos a correção requer necessariamente gerar novos dados de sequenciamento). Os tipos de dados tradicionalmente usados para a curadoria pelo time do Sanger estão resumidos na Tabela 2 do artigo de [Howe e colaboradores (2020)](https://doi.org/10.1101/2020.08.12.247734). Para plotar todos os dados simultaneamente contra o genoma a ser curado, o time utiliza o navegador de genoma *gEVAL*.  

<img src="https://user-images.githubusercontent.com/22843614/90806533-87b5f280-e2f3-11ea-8b2e-410b98f31016.png">  
Exemplo de inconsistência detectada na montagem do genoma de uma ave utilizando o programa gEVAL. Fonte: Howe et al., 2020

### 2.1 Etapas da pipeline de curadoria do time do Instituto Sanger  
O processo de curadoria geralmente parte de uma montagem onde haplótipos tenham sido separados (utilizando uma ferramenta como o *purge_dups*), *scaffold* tenha sido feito baseado em dados de longo alcance (*long-range data*, como por exemplo dados de Hi-C) e a montagem tenha sido polida (*polished*). A partir disso então o processo de curadoria procede com as seguintes etapas:  
1. Detecção automática de eventuais contaminações (de acordo com a Tabela 1 do próprio [artigo](https://doi.org/10.1101/2020.08.12.247734)), combinada com remoção de sequências terminais de N's (*trailing Ns*). Checagem manual dos resultados é feita para previnir remoção errônea de regiões potencialmente derivadas de transferência horizontal;  
2. Dados disponíveis são carregados no *gEVAL* (construído baseado no *framework* do Ensembl). Quais análises serão carregadas no *gEVAL* dependem da disponibilidade para a espécie, mas tipicamente são os tipos listados na Tabela 2 do próprio [artigo](https://doi.org/10.1101/2020.08.12.247734). O processo de análises + carregamento das análises no *gEVAL* tipicamente dura em torno de 3 dias para cada 1 Gb; 
3. Curadores experientes utilizam o banco de dados e a visualização do *gEVAL*, bem como mapas de Hi-C (esses gerados fora do *gEVAL* e visualizados usando os programas *HiGlass* ou *pretext*) para buscar por eventuais discordâncias e decidir se e como ajustar a montagem com base nos dados disponíveis. Em casos raros as informações disponíveis via *gEVAL* e mapas de Hi-C não são suficientes para decidir se uma mudança é necessária;  
4. Curadores utilizam ferramentais adicionais como [gap5](https://academic.oup.com/bioinformatics/article/26/14/1699/178142) para análise aprofundada de leituras (*reads*) mapeadas ou [Genomicus](https://academic.oup.com/nar/article/46/D1/D816/4566017);  
5. Curadores propõem intervenções na montagem, tais como quebra (*break*) e junção (*join*) de sequências, mudança de ordem e orientação de scaffolds e contigs, e remoção de falsas duplicações. Desemaranhar (*to detangle*) colapsos de sequências (quando por exemplo uma sequência de repetições é representada por uma única unidade repetitiva) é possível atualmente apenas quando dados adicionais podem ser suportados para re-montagem local.

# Referências  
## Juicebox Assembly Tools  
* [Artigo](https://www.biorxiv.org/content/10.1101/254797v1.full#F2) 
* [Repositório Github](https://github.com/aidenlab/Juicebox/wiki/Juicebox-Assembly-Tools)
 
## Artigo curadoria manual time do Instituto Sanger  
* [Howe et al., 2020](https://doi.org/10.1101/2020.08.12.247734)  

## Navegador *gEVAL*  
* [Artigo](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4978925/)  
* [Página no Sanger](https://geval.sanger.ac.uk/index.html)  
* [Repositório Github](https://github.com/wchow/wtsi-geval-plugin)
