# Tabela-Hash-para-Deduplica-o-de-Dataset-
Trabalho de Estruturas de Dados



import csv

class TabelaHash:
    def __init__(self, tamanho=101, funcao_hash=None):
        self.tamanho = tamanho
        self.tabela = [[] for _ in range(tamanho)]  # Encadeamento exterior
        self.funcao_hash = funcao_hash if funcao_hash else self.hash_divisao

    def hash_divisao(self, chave):
        """Função de hash padrão: método da divisão"""
        return int(chave) % self.tamanho

    def inserir(self, chave, dado):
        indice = self.funcao_hash(chave)
        bucket = self.tabela[indice]
        # Verificar duplicatas
        for item in bucket:
            if item[0] == chave:
                return False  # Duplicado detectado
        bucket.append((chave, dado))
        return True

    def buscar(self, chave):
        indice = self.funcao_hash(chave)
        bucket = self.tabela[indice]
        for item in bucket:
            if item[0] == chave:
                return item[1]
        return None

    def remover(self, chave):
        indice = self.funcao_hash(chave)
        bucket = self.tabela[indice]
        for i, item in enumerate(bucket):
            if item[0] == chave:
                del bucket[i]
                return True
        return False

    def obter_todos(self):
        """Retorna lista com todos os dados não duplicados"""
        resultado = []
        for bucket in self.tabela:
            for chave, dado in bucket:
                resultado.append(dado)
        return resultado

# =========================
# Exemplo para deduplicar CSV
# =========================

def deduplicar_csv(caminho_csv, chave_coluna, saida_csv=None):
    tabela = TabelaHash(tamanho=211)  # Tamanho primo reduz colisões

    # Ler arquivo CSV
    with open(caminho_csv, newline='', encoding='utf-8') as csvfile:
        leitor = csv.DictReader(csvfile)
        for linha in leitor:
            chave = linha[chave_coluna]
            tabela.inserir(chave, linha)

    registros_unicos = tabela.obter_todos()

    # Se for para salvar em novo CSV
    if saida_csv:
        with open(saida_csv, 'w', newline='', encoding='utf-8') as csvout:
            escritor = csv.DictWriter(csvout, fieldnames=registros_unicos[0].keys())
            escritor.writeheader()
            for reg in registros_unicos:
                escritor.writerow(reg)

    return registros_unicos

if __name__ == "__main__":
    caminho_csv = input("Digite o caminho do arquivo CSV: ").strip()
    chave_coluna = input("Digite o nome da coluna chave (para deduplicação): ").strip()
    saida_csv = input("Digite o caminho para o arquivo de saída (ou deixe em branco para não salvar): ").strip()
    saida_csv = saida_csv if saida_csv else None

    registros = deduplicar_csv(caminho_csv, chave_coluna, saida_csv)
    print(f"{len(registros)} registros únicos encontrados.")
