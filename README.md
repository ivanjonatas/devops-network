# devops-network
Repositorio com os conhecimentos minimos de redes para utilizacao no dia a dia. 



# Camada TCP/IP

![example](img/tcp-ip.png)

Nas primeiras camadas temos alguns protocolos que sao usados no dia a dia ou que e preciso ter conhecimento deles.

camada de aplicação: HTTP, Telnet, DNS, PING, FTP, SSH, POP3, SMTP, IMAP, TLS;

camada de transporte: TCP, UDP;

camada de rede: IP, IPsec, ICMP;



## Camada Aplicacao

camada de aplicação: HTTP, Telnet, DNS, PING, FTP, SSH, POP3, SMTP, IMAP;

**SMTP**: protocolo que faz a conexao do servidor de email com o email cliente da maquina;

**POP3**: Protocolo para o cliente acessa o servidor de email. Esse protocolo baixa todos os email do servidor, armazenando os email na maquina do cliente;

**IMAP**: Protocolo para o cliente acessa o servidor de email. Esse protocolo faz apenas uma copia dos email que estao no servidor, deixando uma copiado no servidor;

**TLS**: E utilizado na camada de aplicacao, assim o cabecalho do tcp/ip nao eh protegido, protegendo apenas os dados da mensagem.
- O TLS e dividido em duas partes handshake e record;
- O handshake e responsavel pela autenticaçao entre o cliente o servidor e o record  pela integridade e confidencialidade da mensagem.



## Camada Transporte

Na camada de transporte existe o protocolo UPD e o TCP. Essa sao as diferencas entre eles:

**TCP**: A comunicacao com esse protocolo e feita utilizando o metodo handshake onde as duas maquinas precisam confirma comunicacao. So depois da confirmacao entre ambas que o processo de enviou de pacotes e iniciado. Nesse protocolo a perda de pacote e notada e caso precise e reenviado novamente.

**UDP**: Nesse protocolo o pacote e apenas enviado, sem precisa as duas maquinas valida comunicacao, alem de o monitoramento de perda de pacote nao existir.


## Camada Redes

camada de rede: IP, IPsec, ICMP, IGMP

### IP
O protocolo IP e o responsavel por receber os pacores do procotoco TCP/UDP e enviar. O sistema de enderecamento utilizado na camada de redes e o endereco logico e endereco fisico.

Com o enderecamento logico nao e necessario saber o enderecamento de ip fisico da maquina, o protocolo ARP e RARP sao responsaveis por receber o ip logico e identificar qual ip fisico corresponde. *Contudo o protocolo RARP foi subistituido pelo protocolo DHCP*

**Tipos de enderecamento IPV4**
unicast: unico ip
broadcast: 
anycast: varios servidores com o mesmo ip, muito usado no CDN. usado junto com o protocolo BGP

### IPsec

Consegue fazer a protege o cabecalho do TCP alem de fazer a autenticacao, integridade, confidencialidade dos pacotes.
O IPsec substitui o cabecalho do IP.

### ICMP

Mensagem do ICMP sao geradas diretamenta da camada de rede da origem, assim nao tem cabeçalho de TCP e IP.

```bash
Comando utilizado

# enviando um 'echo' para validar acesso ao destino.
ping $destino(ip/dns)


# Motirando o caminho do 'eco' ate destino.
traceroute $destino(ip/dns)
```

- Apos executar o ping e possivel identifica qual SO esta sendo usado no destino, baseado no TTL, conforme a tabela abaixo:
UNIX: 64
Windows: 128
AIX/Solaris/Cisco: 254/255

O traceroute ira registra todos os saltos feito pelo echo ate chegar o destino final.

A fraquimentacao do dado e com base no tamanho do MTU(Maximum Transmission Unit), caso o pacote seja maio que o mtu estabelecio o pacote sera fraquementado para ser enviado.



### Servidor DHCP

servidor DHCP e{} quem distribui toda as informacoes da rede, o dhcp configurado de forma dinamica o endereco da maquina fisica de forma dinamica.
    a maquina pode mudar o IP de forma dinamica.
    quando uma maquina se conecta a rede ele soliticar informacoes da rede ao  DHCP que enviar as infos de ip,mascara e gateway



### Roteamento

O roteador quando recebe o pacote faz o encaminhamento com base na rota mais curta ate o destino, isto e o caminho com menos saltos. Outra forma de roteamento e com base no caminho mais rapido. 

O roteamento tem procolos internos e externos. o procolo interno configura toda regra de roteamento dentro da rede antes de ir para internet, em redes domesticas esses protocos agem dentro dos provedores de internet, escolhando o melhor caminho para o pacote sair para internet.

O protocolo externo e o responsavel por encontrar o melhor caminho para chegar ao servidor.

**BGP**: usa protocolo TCP, protocolo de confirmacao. Esse protocolo definir politicas de rota para chegar no destino final.



# Netfilter iptables

Iptables tem configuracoes feita atraves das tabelas e as chain. cada table tem suas chain.

**filter**: INPUT, OUTPUT, FORWARDING

**Nat**: INPUT, OUTPUT, POSTROUTING, PREROUTING

**mangle**: INPUT, OUTPUT, PREROUTING, POSTROUTING, FORWARDING


especial value:
ACCEPT
DROP
REJECT


## comandos simples

- BLOQUEANDO PING PARA UM IP
        sudo iptables -I INPUT -s 192.168.56.1 -p icmp --icmp-type echo-request -j DROP

- BLOQUEANDO PING PARA QUALQUER MAQUINA
        sudo iptables -I INPUT -p icmp --icmp-type echo-request -j DRO

        obs.: as maquinas que solicitar ping na maquina, chegar ate a mesma mas nao recebe retorno.

- EXCLUINDO UMA REGRA DO INPUT-comando -D
        sudo iptables -D INPUT 1

## Salvando regras IPTABLES

Todas as regras criada no iptables precisam ser persistidas, pois caso o servidor reiniciar as regras atual seram perdidas.

Para pode ser usado o iptables-persistent para deixar salvo as regras em um arquivo. esse servico restart as regrasa quando o servidor for reiniciado.


## tcpdump

monitoramento baseado em:
- protocolo
- porta
- destino

E possivel usar os operadores AND e OR

e possivel armazena a captura em um arquivo e depois ler esse arquivo
possivel filtra a captura pelo tamanho do pacote
dizer o maximo de pacote que quer captura. depois de atigir o numero o monitoramento e fechado

```bash
Comando utilizado

# Identificando as redes disponivel para o tcpdum
sudo tcpdump -D


# Monitorando um protocolo d euma rede especifica
sudo tcmdump -i $interface_de_rede -p $protocolo 


# Salvando monitramento em um arquivo
sudo tcpdump -i $interface_de_rede -w dump-network.cap


# Lendo o arquivo salvo
sudo tcpdump -r dump-network.cap


# Limitando a quantidade de pacotes capturado
sudo tcpdump -c 20 -i $interface_de_rede


# Filtrando monitoramento pelo tamanho do pacote em bytes
sudo tcpdump -i $interface_de_rede greater 100


# monitorando pela porta
sudo tcpdum -i $interface_de_rede port $number_port


# Monitorando trafego pelo destino
sudo tcmpdum -i $interface_de_rede dst $ip_destino


# Usando operador AND
sudo tcmpdum -i $interface_de_rede dst $ip_destino and icmp


# Usando operador OR
sudo tcpdump -i enp0s8 dst $ip_destino and port 22 or -p icmp


```




[2023-02-10T15:19:36.6943+00:00] [OHS] [ERROR:32] [OH99999] [ossl] [host_id: node-http] [host_addr: 192.168.56.103] [pid: 3871] [tid: 140190542718720] [user: ivan] [VirtualHost: node-http:0] OHS:2171 NZ Library Error: Unknown error
[2023-02-10T15:19:37.8218+00:00] [OHS] [ERROR:32] [OH99999] [ossl] [client_id: 192.168.56.103] [host_id: node-http] [host_addr: 192.168.56.103] [pid: 3871] [tid: 140190525933312] [user: ivan] [VirtualHost: node-http:0] OHS:2079 Client SSL handshake error, nzos_Handshake returned 29005(server node-http:443)