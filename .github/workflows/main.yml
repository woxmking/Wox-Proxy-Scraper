import requests
from bs4 import BeautifulSoup
import re
import time


headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36',
}

def salvar_proxy_em_arquivo(proxy, tipo):
    if tipo == 'socks5':
        arquivo = f'socks5.txt'
        formato = '(IP):%d:(PORT)'
    elif tipo == 'https':
        arquivo = f'https.txt'
        formato = '%s:%d' % proxy['url']
    else:
        return False 

    with open(arquivo, 'a') as arquivo_texto:
        if isinstance(proxy, dict):
            conteúdo = formato % (proxy['ip'], proxy['port'])
            arquivo_texto.write(f'{conteúdo}\n')
        else:
            return False  

def pegar_proxies_do_site(url, tipo):
    try:
        response = requests.get(url, headers=headers)
        if not response.status_code == 200:
            raise Exception(f'Erro de conexão: {response.status_code} - {response.text}')
        
        soup = BeautifulSoup(response.text, 'html.parser')
        proxies = soup.find_all('div', class_=f'{tipo}-proxy')

        if not proxies:
            raise Exception(f'Não foram encontrados proxies do tipo {tipo}')

        for proxy in proxies:
            info = []
            for tag in proxy.find_all('span'):
                text = tag.get_text()
                if 'IP Address' in text or 'IP:' in text:
                    ip = re.sub(r'\s+', '', text)
                    info.append(('ip', ip))
                elif 'Port' in text or 'PORT' in text:
                    port = int(re.search(r'\d+', text).group())
                    info.append(('port', port))

            
            salvar_proxy_em_arquivo({'url': f'{info[0][1]}:{info[1][1]}', 'ip': info[0][1], 'port': info[1][1]}, tipo)

    except Exception as e:
        print(f'Erro ao pegar proxies do site: {e}')
        return False


sites = [
    ('https://www.proxyNova.com', 'proxy-list'),
    ('https://www.sslproxies.org', 'https-proxies'),
    ('https://www.my-proxylist.com', 'socks5-proxies'),
]

if __name__ == '__main__':
    print('Início do script...')
    
    for site_url, type_proxy in sites:
        print(f'Pesquisando em: {site_url}')
        time.sleep(1)  # Evita timeout
        pegar_proxies_do_site(site_url, type_proxy)
    
    print('Finalização do script. Arquivos salvo com sucesso!')
