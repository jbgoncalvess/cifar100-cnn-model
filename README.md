# Classificação de Imagens CIFAR-100 com CNN Customizada

> Trabalho Prático de Redes Neurais Convolucionais (CNNs) para a disciplina de **IA na Borda**.
> 
> **Aluno:** Jardel Gonçalves | **Professor:** Dr. Roderval Marcelino

---

## O Desafio: Dataset CIFAR-100
O objetivo deste projeto foi construir e treinar do zero uma arquitetura de Rede Neural Convolucional (CNN) capaz de reconhecer e classificar imagens do complexo dataset **CIFAR-100**. 

Este conjunto de dados impõe um grande desafio: classificar **100 categorias distintas** (como pessoas, animais, veículos e objetos) contando com imagens minúsculas de **32x32 pixels** e apenas **500 imagens de treinamento por classe**. Essa escassez de dados, aliada à alta variabilidade visual, torna a rede extremamente suscetível ao *overfitting* (memorização de ruídos). Para superar isso, o modelo foi desenhado com base em arquiteturas modernas e literatura científica.

---

## Arquitetura e Inovações do Modelo

Para solucionar o problema de *overfitting* e evoluir a arquitetura básica da CNN, foi implementado um conjunto robusto de técnicas avançadas de regularização, otimização e controle dinâmico. 

### 1. Expansão Artificial dos Dados (Data Augmentation)
Para ensinar o modelo a focar nas características reais dos objetos, implementamos um pipeline nativo para criar variações das imagens durante o treino:
* **RandomFlip:** Inversão horizontal.
* **RandomTranslation (10%):** Deslocamentos laterais e verticais.
* **RandomRotation (10%):** Pequenas rotações.
* **RandomContrast (20%):** Alterações dinâmicas na iluminação.

### 2. Evolução Arquitetural Profunda
A rede utiliza **4 Blocos Convolucionais** progressivos (64, 128, 256 e 512 filtros), incorporando:
* **Batch Normalization:** Inserido após cada convolução para normalizar as informações, evitando gradientes desregulados e acelerando a convergência.
* **Dropout Progressivo:** Desliga neurônios aleatoriamente durante o treino (iniciando em 10% e escalando até 40% nas camadas profundas) para forçar a rede a aprender padrões robustos em vez de decorar as imagens.

### 3. Redução Eficiente de Dimensionalidade
* **Global Average Pooling 2D (GAP):** Em substituição à tradicional camada `Flatten`. O GAP reduz drasticamente a quantidade de parâmetros da rede, tirando a média espacial dos mapas de características. Isso tornou o modelo mais leve e focado, mitigando fortemente o risco de *overfitting*.
* **Classificação Final:** Camada Oculta enxuta de 256 neurônios, seguida da saída final com **100 neurônios (ativação Softmax)**.

---

## Estratégia de Treinamento

O treinamento deixou de ser estático e passou a se adaptar ao desempenho da rede em tempo real:

* **Otimizador AdamW & Weight Decay:** A transição para o AdamW permitiu o uso do fator de decaimento de pesos (`weight_decay=1e-4`), penalizando conexões que tentam monopolizar a decisão e distribuindo o aprendizado.
* **Processamento em Lotes (Batch Size):** Configurado para **128**, garantindo estabilidade no cálculo do gradiente e variação saudável.
* **ReduceLROnPlateau (Callback):** Monitora a perda de validação. Se o aprendizado estagnar por 5 épocas, a taxa de aprendizado é cortada pela metade, refinando os pesos na reta final.
* **EarlyStopping (Callback):** Interrompe o treinamento (limite de 120 épocas) ao perceber que o erro de validação parou de melhorar por 10 épocas, restaurando automaticamente a melhor versão histórica do modelo.

---

## Resultados Alcançados

Construir uma CNN do zero sem o uso de *Transfer Learning* (redes pré-treinadas como ResNet/VGG) para o CIFAR-100 é um teste árduo.

Apesar da alta complexidade, o modelo atingiu uma **acurácia de validação/teste entre 65% e 67%**. Este é um resultado extremamente sólido para uma arquitetura construída puramente do zero, provando que a combinação de Data Augmentation, Batch Normalization, GAP e AdamW foi bem-sucedida em extrair padrões complexos de imagens de baixíssima resolução.

O notebook também gera uma **Matriz de Confusão** completa e realiza predições visuais individuais para auditoria qualitativa dos acertos do modelo.

---

## Referências Científicas

A engenharia deste modelo foi diretamente inspirada por avanços documentados na literatura de Deep Learning voltada para o CIFAR-100. As referências abaixo foram fundamentais para guiar as escolhas de hiperparâmetros, camadas de atenção e topologia:

1. [Wavelet‑Attention CNN for image classification](https://link.springer.com/article/10.1007/s00530-022-00889-8) *(Inspiração na mitigação de ruídos)*
2. [B-CNN: Branch Convolutional Neural Network for Hierarchical Classification](https://arxiv.org/abs/1709.09890)
3. [Non-linear Convolution Filters for CNN-based Learning](https://openaccess.thecvf.com/content_iccv_2017/html/Zoumpourlis_Non-Linear_Convolution_Filters_ICCV_2017_paper.html)
4. [Tree-CNN: A hierarchical Deep Convolutional Neural Network for incremental learning](https://www.sciencedirect.com/science/article/pii/S0893608019302710)


---
Este projeto foi desenvolvido como requisito prático da disciplina de **Inteligência Artificial na Borda (IA na Borda)**, pertencente ao **Programa de Pós-Graduação em Tecnologias da Informação e Comunicação (PPGTIC)** da **Universidade Federal de Santa Catarina (UFSC) - Campus Araranguá**.

* **Docente:** Prof. Dr. Roderval Marcelino
* **Discente:** Jardel Gonçalves
* **Tecnologias utilizadas:** Python, Keras/TensorFlow, Google Colab
