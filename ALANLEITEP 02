/* war_missoes.c
   Versão demonstrativa do desafio final: sistema de missões individuais para cada jogador,
   mapa de territórios alocado dinamicamente, ataques simulados por dado, e verificação de missões.
   Autor: (seu nome)
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#define MAX_NOME 30
#define MAX_COR 10

typedef struct {
    char nome[MAX_NOME];
    char cor[MAX_COR]; // cor/posse do território (ex: "Azul", "Vermelha")
    int tropas;
} Territorio;

/* --- Prototipos --- */
void atribuirMissao(char* destino, char* missoes[], int totalMissoes, const char* corJogador);
int verificarMissao(const char* missao, Territorio* mapa, int tamanho, const char* corJogador);
void exibirMissao(const char* missao);
void atacar(Territorio* atacante, Territorio* defensor);
void exibirMapa(Territorio* mapa, int tamanho);
void liberarMemoria(Territorio* mapa, int tamanho, char* missao1, char* missao2);

/* --- Implementacoes --- */

/* Sorteia uma missão e copia para `destino`. `destino` deve ter memória alocada. */
void atribuirMissao(char* destino, char* missoes[], int totalMissoes, const char* corJogador) {
    int idx = rand() % totalMissoes;
    // Formato: [Missão] (texto)
    strcpy(destino, missoes[idx]);
    // NOTA: não adicionamos a cor do jogador na string para manter exibição limpa.
    // A cor do jogador é passada separadamente para a verificação.
}

/* Exibe missão (passagem por valor - recebemos apenas const char*). */
void exibirMissao(const char* missao) {
    printf("Sua missão: %s\n", missao);
}

/* Simula um ataque com rolagem de dado (1..6). Atualiza tropas e posse conforme regras simples.
   Se atacante vence, transfere a cor e metade das tropas (arredondando para baixo) para defensor.
   Caso perca, atacante perde 1 tropa. */
void atacar(Territorio* atacante, Territorio* defensor) {
    if (!atacante || !defensor) return;

    int dadoAtq = (rand() % 6) + 1;
    int dadoDef = (rand() % 6) + 1;
    printf("Rolagem atacante (%s): %d   defensor (%s): %d\n", atacante->nome, dadoAtq, defensor->nome, dadoDef);

    if (dadoAtq > dadoDef) {
        // atacante vence
        int tropasTransferidas = atacante->tropas / 2;
        if (tropasTransferidas < 1) tropasTransferidas = 1;
        defensor->tropas = tropasTransferidas;
        strcpy(defensor->cor, atacante->cor);
        // diminui atacante para restar as tropas transferidas
        atacante->tropas = atacante->tropas - tropasTransferidas;
        if (atacante->tropas < 0) atacante->tropas = 0;
        printf("Ataque bem-sucedido! %s agora pertence a %s com %d tropas.\n",
               defensor->nome, defensor->cor, defensor->tropas);
    } else {
        // atacante perde 1 tropa
        atacante->tropas = (atacante->tropas > 0) ? atacante->tropas - 1 : 0;
        printf("Ataque falhou. %s perdeu 1 tropa (agora %d).\n", atacante->nome, atacante->tropas);
    }
}

/* Verifica se a missão do jogador (missao) foi cumprida no mapa.
   Recebe a cor do jogador para saber o que pertence a ele.
   Implementa verificações simples para tipos de missões pré-definidas.
   Retorna 1 se cumprida, 0 caso contrário.
*/
int verificarMissao(const char* missao, Territorio* mapa, int tamanho, const char* corJogador) {
    if (!missao || !mapa || !corJogador) return 0;

    // Missões suportadas (textos definidos no vetor de missoes)
    // 1) "Conquistar 3 territorios seguidos"
    // 2) "Conquistar 5 territorios"
    // 3) "Eliminar todas as tropas da cor Vermelha"
    // 4) "Controlar todos os territorios de cor Azul"
    // 5) "Possuir 10 tropas no total"

    // Verificação 1: Conquistar 3 territorios seguidos
    if (strstr(missao, "3 territorios seguidos") != NULL) {
        int consec = 0;
        for (int i = 0; i < tamanho; ++i) {
            if (strcmp(mapa[i].cor, corJogador) == 0) {
                consec++;
                if (consec >= 3) return 1;
            } else consec = 0;
        }
        return 0;
    }

    // Verificação 2: Conquistar 5 territorios (posse total)
    if (strstr(missao, "Conquistar 5 territorios") != NULL) {
        int count = 0;
        for (int i = 0; i < tamanho; ++i)
            if (strcmp(mapa[i].cor, corJogador) == 0) count++;
        if (count >= 5) return 1;
        return 0;
    }

    // Verificação 3: Eliminar todas as tropas da cor Vermelha (ou outra cor)
    if (strstr(missao, "Eliminar todas as tropas da cor ") != NULL) {
        // extrair cor alvo da missão
        const char* p = strstr(missao, "Eliminar todas as tropas da cor ");
        char corAlvo[MAX_COR] = "";
        if (p) {
            p += strlen("Eliminar todas as tropas da cor ");
            strncpy(corAlvo, p, MAX_COR-1);
            corAlvo[MAX_COR-1] = '\0';
            // remover possíveis espaços finais/newline
            for (int i = strlen(corAlvo)-1; i >=0; --i) {
                if (corAlvo[i] == '\n' || corAlvo[i] == '\r' || corAlvo[i] == ' ') corAlvo[i] = '\0';
                else break;
            }
            // verificar se existe qualquer tropa com essa cor
            for (int i = 0; i < tamanho; ++i)
                if (strcmp(mapa[i].cor, corAlvo) == 0 && mapa[i].tropas > 0)
                    return 0;
            return 1;
        }
        return 0;
    }

    // Verificação 4: Controlar todos os territorios de cor Azul (i.e. conquistar todos territórios que inicialmente tinham certa cor)
    if (strstr(missao, "Controlar todos os territorios de cor ") != NULL) {
        const char* p = strstr(missao, "Controlar todos os territorios de cor ");
        char corAlvo[MAX_COR] = "";
        if (p) {
            p += strlen("Controlar todos os territorios de cor ");
            strncpy(corAlvo, p, MAX_COR-1);
            corAlvo[MAX_COR-1] = '\0';
            for (int i = 0; i < tamanho; ++i) {
                // interpreta: se território originalmente tinha cor corAlvo (neste demo inicial, a cor atual é usada)
                // condição da missão: o jogador deve ter todos os territórios que têm cor corAlvo (nesta versão, verifica se TODOS os territorios cuja cor ORIGINAL = corAlvo agora pertencem ao jogador)
                // Para simplificar, aqui verificamos se não existe território com corAlvo diferente da corJogador
                if (strcmp(mapa[i].cor, corAlvo) == 0 && strcmp(corAlvo, corJogador) != 0) {
                    // ainda existe território da cor alvo que não pertence ao jogador
                    return 0;
                }
            }
            // Se nenhum território com corAlvo diferente do corJogador foi encontrado, cumprir
            return 1;
        }
        return 0;
    }

    // Verificação 5: Possuir 10 tropas no total
    if (strstr(missao, "Possuir 10 tropas no total") != NULL) {
        int soma = 0;
        for (int i = 0; i < tamanho; ++i)
            if (strcmp(mapa[i].cor, corJogador) == 0) soma += mapa[i].tropas;
        if (soma >= 10) return 1;
        return 0;
    }

    // Missao desconhecida -> não cumprida
    return 0;
}

/* Exibe o mapa (lista de territórios) */
void exibirMapa(Territorio* mapa, int tamanho) {
    printf("Mapa atual:\n");
    printf("%-3s %-20s %-10s %s\n", "ID", "Territorio", "Dono", "Tropas");
    for (int i = 0; i < tamanho; ++i) {
        printf("%-3d %-20s %-10s %d\n", i, mapa[i].nome, mapa[i].cor, mapa[i].tropas);
    }
    printf("\n");
}

/* Libera memória alocada (territórios e missões) */
void liberarMemoria(Territorio* mapa, int tamanho, char* missao1, char* missao2) {
    if (mapa) free(mapa);
    if (missao1) free(missao1);
    if (missao2) free(missao2);
}

/* --- Programa Principal (demo interativo simples) --- */
int main() {
    srand((unsigned int)time(NULL));

    // Vetor de missoes (strings literais)
    char* missoes[] = {
        "Conquistar 3 territorios seguidos",
        "Conquistar 5 territorios",
        "Eliminar todas as tropas da cor Vermelha",
        "Controlar todos os territorios de cor Azul",
        "Possuir 10 tropas no total"
    };
    int totalMissoes = sizeof(missoes)/sizeof(missoes[0]);

    // Criar mapa (alocacao dinamica de Territorio)
    int nTerritorios = 8;
    Territorio* mapa = (Territorio*) calloc(nTerritorios, sizeof(Territorio));
    if (!mapa) {
        printf("Erro de alocação de memória.\n");
        return 1;
    }

    // Inicializar um mapa simples (nomes, cores e tropas)
    // Cores iniciais misturadas (Azul, Vermelha, Neutra)
    const char* nomes[] = {"Porta Norte", "Jardim", "Sala de Jantar", "Biblioteca", "Cozinha", "Torre", "Sala Oeste", "Pátio"};
    const char* coresIni[] = {"Azul", "Vermelha", "Azul", "Neutra", "Vermelha", "Azul", "Neutra", "Vermelha"};
    int tropasIni[] = {3, 2, 4, 1, 3, 2, 1, 4};

    for (int i = 0; i < nTerritorios; ++i) {
        strncpy(mapa[i].nome, nomes[i], MAX_NOME-1);
        mapa[i].nome[MAX_NOME-1] = '\0';
        strncpy(mapa[i].cor, coresIni[i], MAX_COR-1);
        mapa[i].cor[MAX_COR-1] = '\0';
        mapa[i].tropas = tropasIni[i];
    }

    // Jogadores: apenas demo com 2 jogadores
    char corJogador1[MAX_COR] = "Azul";
    char corJogador2[MAX_COR] = "Vermelha";

    // Alocar missões dinamicamente para cada jogador (malloc)
    char* missao1 = (char*) malloc(100 * sizeof(char));
    char* missao2 = (char*) malloc(100 * sizeof(char));
    if (!missao1 || !missao2) {
        printf("Erro de alocação para missões.\n");
        liberarMemoria(mapa, nTerritorios, missao1, missao2);
        return 1;
    }
    // Atribui missões
    atribuirMissao(missao1, missoes, totalMissoes, corJogador1);
    atribuirMissao(missao2, missoes, totalMissoes, corJogador2);

    // Exibir missão apenas uma vez para cada jogador (interface)
    printf("=== Jogo War - Missões Individuais (Demo) ===\n\n");
    printf("Jogador 1 (cor %s):\n", corJogador1);
    exibirMissao(missao1);
    printf("\nJogador 2 (cor %s):\n", corJogador2);
    exibirMissao(missao2);
    printf("\nIniciando jogo...\n\n");

    // Loop de jogo simples: turnos alternados até missão cumprida
    int turno = 0;
    int maxTurns = 200; // limite de segurança
    int vencedor = 0; // 0 = nenhum, 1 = jogador1, 2 = jogador2

    while (turno < maxTurns) {
        int jogadorAtual = (turno % 2 == 0) ? 1 : 2;
        char* corAtual = (jogadorAtual == 1) ? corJogador1 : corJogador2;
        char* missaoAtual = (jogadorAtual == 1) ? missao1 : missao2;

        printf("----- Turno %d: Jogador %d (cor %s) -----\n", turno+1, jogadorAtual, corAtual);
        exibirMapa(mapa, nTerritorios);

        // Simples: pedir ao jogador escolher territorio atacante e defensor via indices
        int idxAtacante, idxDefensor;
        printf("Escolha o ID do territorio atacante (pertencente a '%s'), ou -1 para pular ataque: ", corAtual);
        if (scanf("%d", &idxAtacante) != 1) {
            // entrada invalida: limpar e pular
            while (getchar() != '\n');
            printf("Entrada inválida. Pulando turno.\n");
            idxAtacante = -1;
        }

        if (idxAtacante >= 0) {
            if (idxAtacante < 0 || idxAtacante >= nTerritorios) {
                printf("Indice atacante inválido. Pulando.\n");
            } else if (strcmp(mapa[idxAtacante].cor, corAtual) != 0) {
                printf("Você não pode atacar com um território que não é seu.\n");
            } else if (mapa[idxAtacante].tropas <= 0) {
                printf("Território atacante não tem tropas suficientes.\n");
            } else {
                printf("Escolha o ID do territorio defensor: ");
                if (scanf("%d", &idxDefensor) != 1) {
                    while (getchar() != '\n');
                    printf("Entrada inválida. Pulando ataque.\n");
                } else {
                    if (idxDefensor < 0 || idxDefensor >= nTerritorios) {
                        printf("Indice defensor inválido. Pulando.\n");
                    } else if (strcmp(mapa[idxDefensor].cor, corAtual) == 0) {
                        printf("Você não pode atacar seu próprio território.\n");
                    } else {
                        // realizar ataque
                        atacar(&mapa[idxAtacante], &mapa[idxDefensor]);
                    }
                }
            }
        } else {
            printf("Jogador escolheu não atacar neste turno.\n");
        }

        // Ao final do turno, verificar se a missão de qualquer jogador foi cumprida (verificação silenciosa)
        if (verificarMissao(missao1, mapa, nTerritorios, corJogador1)) {
            vencedor = 1;
            break;
        }
        if (verificarMissao(missao2, mapa, nTerritorios, corJogador2)) {
            vencedor = 2;
            break;
        }

        turno++;
        printf("\n");
    }

    if (vencedor == 1) {
        printf("=== Missão cumprida! Jogador 1 (cor %s) venceu! ===\n", corJogador1);
        printf("Missão: %s\n", missao1);
    } else if (vencedor == 2) {
        printf("=== Missão cumprida! Jogador 2 (cor %s) venceu! ===\n", corJogador2);
        printf("Missão: %s\n", missao2);
    } else {
        printf("== Fim do jogo: nenhum jogador cumpriu a missão dentro do limite de turnos ==\n");
    }

    // liberar memoria
    liberarMemoria(mapa, nTerritorios, missao1, missao2);
    return 0;
}
