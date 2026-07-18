# Correção do travamento do H2O (`h2o.init()`) causado por IP extra no loopback

## Problema observado

Ao executar o H2O através do Python:

```python
import h2o

print("antes init", flush=True)

h2o.init()

print("depois init", flush=True)
````

a execução ficava travada após:

```
Connecting to H2O server at http://127.0.0.1:54321 ... successful.
```

O comando nunca chegava ao próximo `print`:

```
depois init
```

O problema ocorria tanto no Spyder quanto diretamente pelo terminal.

---

# Diagnóstico

## 1. Verificar se existe um processo H2O ativo

Primeiro, remover possíveis instâncias antigas:

```bash
pkill -f h2o
pkill -f H2O
```

Confirmar:

```bash
jps -l
```

O resultado esperado é somente:

```
Jps
```

---

## 2. Executar o H2O manualmente

Executar o servidor diretamente:

```bash
java -jar ~/venv/fms_automl/lib/python3.9/site-packages/h2o/backend/bin/h2o.jar \
-ip 127.0.0.1 \
-web_ip 127.0.0.1 \
-port 54321
```

O servidor iniciava corretamente:

```
H2O started in 2175ms

Open H2O Flow in your web browser:
http://127.0.0.1:54321
```

Porém, observou-se algo estranho:

```
Cloud of size 2 formed

[/127.0.0.1:54321, /54.232.189.113:54321]
```

O H2O estava detectando dois nós:

* `127.0.0.1`
* `54.232.189.113`

O segundo IP não deveria estar associado ao computador local.

---

# Identificação da causa

Verificar interfaces de rede:

```bash
ip addr show lo
```

Resultado problemático:

```
lo:

inet 127.0.0.1/8 scope host lo

inet 54.232.189.113/32 scope global lo
```

O IP:

```
54.232.189.113
```

estava configurado permanentemente na interface de loopback.

O H2O utiliza descoberta automática de rede para formar o cluster.
Esse IP adicional fazia o H2O criar um segundo nó fantasma, causando travamento no cliente Python.

---

# Remoção temporária (teste)

Antes de remover permanentemente, foi realizado o teste:

```bash
sudo ip addr del 54.232.189.113/32 dev lo
```

Confirmar:

```bash
ip addr show lo
```

Agora deveria aparecer somente:

```
inet 127.0.0.1/8 scope host lo
inet6 ::1/128 scope host
```

Após isso:

```bash
pkill -f h2o
```

Executar novamente:

```python
import h2o

print("antes init", flush=True)

h2o.init()

print("depois init", flush=True)
```

Resultado esperado:

```
antes init

Checking whether there is an H2O instance running...

successful.

depois init
```

---

# Remoção permanente

## 1. Verificar configuração do Netplan

Listar arquivos:

```bash
ls /etc/netplan/
```

Exemplo:

```
50-cloud-init.yaml
```

Pesquisar o IP:

```bash
sudo grep -R "54.232.189.113" /etc/netplan/
```

Caso não encontre nada, o IP não estava vindo do Netplan.

---

## 2. Verificar outras configurações de rede

Pesquisar no sistema:

```bash
sudo grep -R "54.232.189.113" /etc/
```

Locais comuns:

```
/etc/network/
/etc/systemd/
/etc/NetworkManager/
```

---

## 3. Verificar NetworkManager

Listar conexões:

```bash
nmcli connection show
```

Ver detalhes:

```bash
nmcli connection show <nome_da_conexao>
```

Procurar entradas como:

```
ipv4.addresses
```

Remover qualquer endereço contendo:

```
54.232.189.113/32
```

---

## 4. Reiniciar a rede

Após remover a configuração:

```bash
sudo systemctl restart NetworkManager
```

ou:

```bash
sudo netplan apply
```

---

# Verificação final

Confirmar que o IP não voltou:

```bash
ip addr show lo
```

Resultado correto:

```
lo:

inet 127.0.0.1/8 scope host lo
inet6 ::1/128 scope host
```

---

# Teste final do H2O

Ativar o ambiente:

```bash
source ~/venv/fms_automl/bin/activate
```

Executar:

```python
import h2o

h2o.init()

print("H2O funcionando")
```

O processo deve finalizar normalmente.
