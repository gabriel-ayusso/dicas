# Dicas de Windows

## WSL 

Dica: Instalar os softwares:

* Windows Terminal
* Power Shell 2

Quando você instalar o wsl2 no Windows e subir, ele vai carregar o processo Vmmem, que tomará boa parte dos recursos da máquina. Para controlar quanto recurso o docker/wsl2 usará, utilizar os comandos:

```shell
wsl --shutdown
notepad $env:USERPROFILE/.wslconfig
```

Os valores serão algo como segue:

```conf
[wsl2]
memory=1GB # Limits VM memory in WSL 2 to 4 GB
processors=2 # Makes the WSL 2 VM use two virtual processors
```