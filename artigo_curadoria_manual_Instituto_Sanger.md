# Sobre o artigo   
O time de curadoria manual do Instituto Sanger lançou um [artigo]((https://doi.org/10.1101/2020.08.12.247734)) descrevendo sua estratégia de curadoria de genomas. O artigo foi publicado em pré-print, com autoria de Howe e colaboradores (2020). Aqui você poderá encontrar alguns destaques das diferentes partes do artigo.  

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



