# PIM-CODIGO-FONTE

#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>

// Variáveis globais para armazenar informações
int totalIngressosInteiros = 0;
int totalIngressosMeia = 0;
int totalIngressosIsencao = 0;
float receitaTotal = 0.0;

// Estrutura para armazenar informações de venda
struct Venda {
    char data[11]; // String para armazenar a data (no formato "dd/mm/yyyy")
    char tipoIngresso[20];
    int quantidade;
    float valorTotal;
    int numeroAleatorio;
    int numAleatorios[100];
};

struct Venda vendas[100];
int numVendas = 0; // Número atual de vendas

// Protótipo da função para escrever as informações em um arquivo CSV
void escreverArquivoCSV();

// Função para exibir o menu que será utilizado pelo funcionário
void exibirMenu() {
    printf("\nMUSEU - Controle de Bilheteria\n");
    printf("1. Comprar Ingressos\n");
    printf("2. Controle Geral\n");
    printf("3. Validar Ingresso\n");
    printf("4. Sair\n");
    printf("Escolha uma opcao: ");
}

// Função para comprar ingressos
void comprarIngresso() {
    float precoInteira = 10;
    float precoMeia = 5;

    int escolha1, escolha2;

    printf("\nDeseja ver quantos temas ?: \n");
    printf("1. Tema\n");
    printf("2. Temas\n");
    printf("3. Temas\n");
    printf("4. Todos os temas\n");
    scanf("%d", &escolha1);

    if (escolha1 < 1 || escolha1 > 4) {
        printf("Escolha invalida.\n");
        return;
    }

    printf("\nEscolha o tipo de ingresso: \n");
    printf("1. Inteira\n");
    printf("2. Meia\n");
    printf("3. Isencao\n");
    scanf("%d", &escolha2);

    if (escolha2 < 1 || escolha2 > 3) {
        printf("Escolha invalida.\n");
        return;
    }

    int quantidade;

    printf("Quantos ingressos foram vendidos? ");
    scanf("%d", &quantidade);

    srand(time(NULL));

    for (int i = 0; i < quantidade; i++) {
        vendas[numVendas].numAleatorios[i] = rand() % 900000 + 100000;
        printf("Numero do ingresso %d: %d\n", i + 1, vendas[numVendas].numAleatorios[i]);
        vendas[numVendas].numeroAleatorio = quantidade;

        numVendas++;
    }

    float valorTotalVenda = 0.0;
    if (escolha2 == 1) {
        totalIngressosInteiros += quantidade;
        valorTotalVenda = quantidade * precoInteira;
    } else if (escolha2 == 2) {
        totalIngressosMeia += quantidade;
        valorTotalVenda = quantidade * precoMeia;
    } else {
        totalIngressosIsencao += quantidade;
    }

    receitaTotal += valorTotalVenda;
    // Salvar informações da venda na estrutura
    char dataVenda[11];
    time_t t;
    struct tm *tm_info;

    time(&t);
    tm_info = localtime(&t);
    strftime(dataVenda, 11, "%d/%m/%Y", tm_info);

    char tipoIngressoStr[20];
    if (escolha2 == 1) {
        strcpy(tipoIngressoStr, "Inteira");
    } else if (escolha2 == 2) {
        strcpy(tipoIngressoStr, "Meia");
    } else {
        strcpy(tipoIngressoStr, "Isencao");
    }

    // Armazenar informações na estrutura
    strcpy(vendas[numVendas].data, dataVenda);
    strcpy(vendas[numVendas].tipoIngresso, tipoIngressoStr);
    vendas[numVendas].quantidade = quantidade;
    vendas[numVendas].valorTotal = valorTotalVenda;
    numVendas++;

    printf("Ingressos comprados com sucesso!\n");

    // Chama a função para escrever as informações no arquivo CSV
    escreverArquivoCSV();
}

// Função para escrever as informações em um arquivo CSV
void escreverArquivoCSV() {
    FILE *arquivo;
    arquivo = fopen("vendas.csv", "w");

    if (arquivo == NULL) {
        printf("Erro ao abrir o arquivo vendas.csv\n");
        return;
    }

    fprintf(arquivo, "Data, Tipo Ingresso, Quantidade, Valor Total\n"); // Escreve o cabeçalho

    for (int i = 0; i < numVendas; i++) {
        if (strlen(vendas[i].data) > 0 && strlen(vendas[i].tipoIngresso) > 0) {
            fprintf(arquivo, "\"%s\", \"%s\", %d, %.2f\n", vendas[i].data, vendas[i].tipoIngresso, vendas[i].quantidade, vendas[i].valorTotal);
        }
    }

    float valorTotalInteira = 0.0;
    float valorTotalMeia = 0.0;

    for (int i = 0; i < numVendas; i++) {
        if (strlen(vendas[i].tipoIngresso) > 0) {
            if (strcmp(vendas[i].tipoIngresso, "Inteira") == 0) {
                valorTotalInteira += vendas[i].valorTotal;
            } else if (strcmp(vendas[i].tipoIngresso, "Meia") == 0) {
                valorTotalMeia += vendas[i].valorTotal;
            }
        }
    }

    fprintf(arquivo, "Soma dos Valores (Inteira) %.2f\n", valorTotalInteira);
    fprintf(arquivo, "Soma dos Valores (Meia) %.2f\n", valorTotalMeia);
    fprintf(arquivo, "Valor Total Geral %.2f\n", valorTotalInteira + valorTotalMeia);

    fclose(arquivo);
}

// Função para exibir as estatísticas gerais
void exibirEstatisticas() {
    printf("Estatisticas do Museu: \n");
    printf("Total de ingressos inteiros vendidos: %d\n", totalIngressosInteiros);
    printf("Total de ingressos meia-entrada vendidos: %d\n", totalIngressosMeia);
    printf("Total de ingressos com isencao: %d\n", totalIngressosIsencao);
    printf("Total de receita: R$ %.2f\n", receitaTotal);
}

// Função para validar o ingresso
void validquest() {
    int numdigitado;

    printf("Por favor, insira o numero do seu ingresso para que possamos iniciar o questionario: \n");
    scanf("%d", &numdigitado);

    int ingressoValidado = 0;

     for (int i = 0; i < numVendas; i++) {
        for (int j = 0; j < vendas[i].numeroAleatorio; j++) {
            if (numdigitado == vendas[i].numAleatorios[j]) {
                ingressoValidado = 1;
                break; // Se um ingresso válido for encontrado, saia do loop
            }
        }
        if (ingressoValidado) {
            break; // Se um ingresso válido for encontrado, saia do loop
        }
    }

    if (ingressoValidado) {
        printf("Ingresso validado, vamos para o proximo passo.\n");
    } else {
        printf("Numero de ingresso incorreto.\n");
    }
}

int main() {
    int opcao;

    do {
        exibirMenu();
        scanf("%d", &opcao);

        switch (opcao) {
            case 1:
                comprarIngresso();
                break;
            case 2:
                exibirEstatisticas();
                break;
            case 3:
                validquest();
                break;
            case 4:
                printf("Obrigado por usar o sistema de controle de bilheteria do museu!\n");
                break;
            default:
                printf("Opção invalida. Tente novamente.\n");
        }
    } while (opcao != 4);

    return 0;
}
