# Sobre o artigo   
O time de curadoria manual do Instituto Sanger lançou um [artigo](https://doi.org/10.1101/2020.08.12.247734) descrevendo sua estratégia de curadoria de genomas. O artigo foi publicado em pré-print, com autoria de Howe e colaboradores (2020). Aqui você poderá encontrar alguns destaques das diferentes partes do artigo.  

## 1. Background  
### 1.1 Curadoria adiciona significativo valor à montagem  
* A despeito dos avanços recentes nas tecnologias de sequenciamento e programas de bioinformática, a montagem automatizada de genomas eucarióticos ainda incorre em uma série de erros, nomeadamente: duplicações (*duplications*), colapsos (*collapses*), junções errôneas (*misjoins*) e junções perdidas (*missed joins*);  
* Esses erros são geralmente causados por regiões mais complexas de montar do genoma, especificamente regiões com alta heterozigosidade e/ou conteúdo repetitivo. Nesses casos, tanto os programas da etapa de montagem quando da etapa de *scaffolding* podem acabar se confundindo, ocasionando problemas na etapa de construção dos *contigs* ou na etapa de agrupamento e ordenamento dos contigs dentro dos *scaffolds*;  
* Como consequência, até os chamados genomas de alta qualidade ou genomas platinum podem sofrer de centenas a milhares de erros de montagem, impactando pesquisas que venham a utilizar esses genomas como recurso de estudo;  
* Uma forma de resolver esses problemas é através de uma análise aprofundada onde diferentes conjuntos de dados disponíveis para o indivíduo que gerou o genoma ou para a espécie são utilizados para buscar por inconsistências na montagem;  
* Já existem uma série de ferramentas que permitem a inspeção de uma montagem utilizando diferentes conjuntos de dados, porém, diferentes ferramentas possibilitam a inspeção com diferentes conjuntos de dados, impedindo o cenário ideal onde todos esses conjuntos seriam analisados simultaneamente;  
* Recentemente, pesquisadores do Instituto Sanger lançaram um navegador de avaliação de genomas (*genoma browser*) chamado *gEVAL*, capaz de plotar simultaneamente diferentes tipos de dados contra uma montagem, facilitando uma inspeção guiada por múltiplas evidências.  

## 2. Resultados  
### 2.1 Sobre a pipeline de curadoria manual do Instituto Sanger  
* O Time de Informática do Genoma Referência (*Genome Reference Informatics Team* - GRIT) posssui uma pipeline de curadoria de montagens que foi desenvolvida para entregar montagens de alta qualidade para o Consórcio de Genoma Referência (*Genome Reference Consortium* - GRC), para o Projeto de Genomas de Vertebrados (*Vertebrate Genomes Projetc* - VGP) e Projeto Darwin Árvore da Vida (*Darwin Tree of Life Project* - DToL);  
* A pipeline automatiza processos de coleta de dados e análise computacional para descontaminação, validação e correção de montagens, utilizando todos os dados disponíveis de bancos de dados públicos e internos;  
* As análises são então disponibilizadas para experientes curadores de genomas, que avaliam os resultados e implementam eventuais mudanças na montagem;  
* Após as correções manuais aplicadas, a nova montagem é gerada de forma automatizada; 
* Central para esse processo de curadoria é o navegador de avaliação de genomas *gEVAL*, que permite visualização de discordância entre a montagem atual e múltiplo conjunto de dados, viabilizando a detecção de possíveis erros de montagem;  
* A pipeline do GRIT está fortemente amarrada à estrutura de dados interna do Instituto Sanger e por isso não é portátil, mas é descrita aqui como um exemplo bem sucedido de implementação que mescla processos automatizados e manuais para melhorar a qualidade das montagens em uma escala de tempo aceitável para projetos de alto rendimento (*high-throughput*)  
### 2.2 Descrição da pipeline  
O processo de curadoria geralmente parte de uma montagem onde haplótipos tenham sido separados (utilizando uma ferramenta como o *purge_dups*), *scaffold* tenha sido feito baseado em dados de longo alcance (*long-range data*, como por exemplo dados de Hi-C) e a montagem tenha sido polida (*polished*). A partir disso então o processo de curadoria procede com as seguintes etapas:   
1. Detecção automática de eventuais contaminações (de acordo com a Tabela 1 do próprio [artigo](https://doi.org/10.1101/2020.08.12.247734)), combinada com remoção de sequências terminais de N's (*trailing Ns*). Checagem manual dos resultados é feita para previnir remoção errônea de regiões potencialmente derivadas de transferência horizontal;  
2. Dados disponíveis são carregados no *gEVAL* (construído baseado no *framework* do Ensembl). Quais análises serão carregadas no *gEVAL* dependem da disponibilidade para a espécie, mas tipicamente são os tipos listados na Tabela 2 do próprio [artigo](https://doi.org/10.1101/2020.08.12.247734). O processo de análises + carregamento das análises no *gEVAL* tipicamente dura em torno de 3 dias para cada 1 Gb; 
3. Curadores experientes utilizam o banco de dados e a visualização do *gEVAL*, bem como mapas de Hi-C (esses gerados fora do *gEVAL* e visualizados usando os programas *HiGlass* ou *pretext*) para buscar por eventuais discordâncias e decidir se e como ajustar a montagem com base nos dados disponíveis. Em casos raros as informações disponíveis via *gEVAL* e mapas de Hi-C não são suficientes para decidir se uma mudança é necessária;  
4. Curadores utilizam ferramentais adicionais como [gap5](https://academic.oup.com/bioinformatics/article/26/14/1699/178142) para análise aprofundada de leituras (*reads*) mapeadas ou [Genomicus](https://academic.oup.com/nar/article/46/D1/D816/4566017);  
5. Curadores propõem intervenções na montagem, tais como quebra (*break*) e junção (*join*) de sequências, mudança de ordem e orientação de scaffolds e contigs, e remoção de falsas duplicações. Desemaranhar (*to detangle*) colapsos de sequências (quando por exemplo uma sequência de repetições é representada por uma única unidade repetitiva) é possível atualmente apenas quando dados adicionais podem ser suportados para re-montagem local; 

#### 2.2.1 Tempo exigido para curadoria  
* Para projetos com necessidade de alta performance (*high-throughput*) como por exemplo VGP e DToL, a curadoria é feita geralmente restrita a uma solução de 100 kb, o que permite um curador experiente a completar um processo de curadoria de 1 Gb de sequências em cerca de 3 dias;  
* Para projetos sem restrições de tempo e focados em genomas referências únicos, como por exemplo o GRC, não existe limite resolução para a curadoria.

#### 2.2.2 Princípios básicos do *gEVAL*
* Durante o processo de curadoria com *gEVAL*, os scaffolds são divididos em componentes de igual tamanho, com sua ordem a orientação salvas em um *path file* sob controle de versão, onde o nome do componente, sua orientação e nome do scaffold são listados. Se qualquer alteração for necessária durante a curadoria, os curadores simplesmente alteram a ordem/orientação do componente no *path file*; 
* Se necessário, os componentes podem ser quebrados utilizando *scripts* sob medida (*bespoke*) para criar novos componentes e armazená-los no banco de dados do *gEVAL*. Após a curadoria ser finalizada, os componentes são processados automaticamente para gerar a versão final da montagem para submissão. Todo o processo de curadoria é registrado, com um histórico das edições aplicadas mantido.  

### 2.3 Impacto da curadoria em projetos de alta performance (*high-throughphut*)
* Durante a curadoria de 111 montagens (representando 174 Gb de sequência) para os projetos VGP e DToL, foi aplicada uma média de 221 intervenções por Gb de sequência;  
* Para genomas inicialmente bem fragmentados, o processo de curadoria tende a aumentar o N50, aumentando a contiguidade das montagens; 
* Para genomas *over-scaffolded*, no entanto, o processo de curadoria pode reduzir o N50 até a metade ao, por exemplo, desfazer erros de junção (*misjoins*) detectados. 

## Dúvidas  
* Como é feita a checagem manual após descontaminação (etapa 1 da pipeline) para previnir remoção errônea de regiões potencialmente derivadas de transferência horizontal?  
* Como gap5 e Genomicus se diferenciam do gEVAL? A princípio me parece que todas as informações de mapeamento de reads e de sintenia, visualizadas respectivamente por gap5 e Genomicus, já poderiam ter sido avaliadas usando o gEVAL.
* O que exatamente é o limite de resolução da curadoria e o que significa um limite de 100 kb? 
