
import time
import os
import textwrap

def menu():
    menu = """"
        BEM VINDO AO NOSSO BANCO
    [u] Criar Usuario
    [nc] Nova Conta
    [d] Depositar
    [s] Sacar
    [e] Extrato
    [q] Sair
    => """
    return (input(menu))

def depositar(valor, saldo, extrato, /):
    if valor > 0:
        saldo += valor
        extrato += f"Deposito: R$ {valor:.2f}"
        print("Deposito realizado com sucesso!!")
    else:
        print("Deposito falhou, por favor insira um valor valido")
    return saldo, extrato

def sacar(*, saldo, valor, limite, numero_saques, limite_saques, extrato):
    #atributos para cada fase
    excedeu_saldo = valor > saldo
    excedeu_limite = valor > limite
    excedeu_saques = numero_saques > limite_saques

    if excedeu_saques:
        print("Limite de saques diarios já alcançados")

    elif excedeu_limite:
        print("Limite de valor para saque alcançado, insira um valor menor.")

    elif excedeu_saldo:
        print("Saldo insuficiente para saque!")
        
    elif valor > 0: 
        saldo -= valor
        numero_saques += 1
        extrato += f"Saque de R$ {valor: .2f}"
        print("Saque realizado!")

    else:
        print("Operação falhou!!")
        
    return saldo, extrato

def exibir_extrato(saldo, /, *, extrato):
    print("Não ocorreu movimentações bancarias" if not extrato else extrato)
    print(f"Saldo: R$ {saldo: .2f}") 

def criar_usuario(usuarios):
    cpf = input("Informe o CPF (somente numeros): ")
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print("Usuario já existente com este CPF!!")
        return
    nome = input("Digite seu nome: ")
    data_nasc = input("Informe a sua data de nascimento (xx-xx-xxxx): ")
    endereco = input("Informe seu endereço (cidade - estado): ")

    usuarios.append({"nome": nome, "data_nasc": data_nasc, "cpf": cpf, "endereco": endereco})

    print("Criando Usuario")
    time.sleep(0.5)
    print("Usuário criado com sucesso!!")

def filtrar_usuario(cpf, usuarios):
    usuario_filtrado = [usuario for usuario in usuarios if usuario["cpf"] == cpf ]
    return usuario_filtrado[0] if usuario_filtrado else None

def criar_conta(agencia, numero_conta, usuarios):
    cpf = input("Informe seu CPF (somente numeros): ")
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print("Conta criada com Sucesso!!")
        return {"agencia": agencia, "numero_conta": numero_conta, "usuario": usuario}
    else:
        print("Usuario não encontrado")


def main():
    #Constante
    LIMITE_SAQUES = 3
    LIMITE = 500
    AGENCIA = "0001"

    #Variaveis
    saldo = 0
    extrato = ""
    usuarios = []
    contas = []
    numero_saques = 0
    
    while True:
        opcao = menu()

        if opcao == "u":
            criar_usuario(usuarios)
        
        elif opcao == "nc":
            numero_conta = len(contas) + 1
            conta = criar_conta(AGENCIA, numero_conta, usuarios)

            if conta:
                contas.append(contas)


        elif opcao == "d":
            valor = float (input("Digite o valor desejado para deposito: R$ "))

            saldo, extrato = depositar (valor, saldo, extrato)
            
        elif opcao == "s":
            valor = float(input("Digite o valor para o saque: R$"))

            saldo, extrato = sacar(
                saldo=saldo,
                valor=valor,
                limite=LIMITE,
                extrato=extrato,
                limite_saques=LIMITE_SAQUES,
                numero_saques=numero_saques,
            )
       
        elif opcao == "e":
            exibir_extrato(saldo, extrato=extrato)

        elif opcao == "q":
            print("Obrigado por utilizar nosso Sistema, até logo :)")
            time.sleep(1)
            break

        else:
            print("Operação invalida!!")
        
main()
