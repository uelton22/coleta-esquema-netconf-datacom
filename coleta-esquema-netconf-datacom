from ncclient import manager
from lxml import etree

import logging
logging.basicConfig(level=logging.DEBUG)

# Configurações do dispositivo
device = {
    'host': '10.0.0.1',
    'port': 830,  # Porta NETCONF padrão
    'username': 'user',
    'password': 'pwd',
    'hostkey_verify': False,
    'device_params': {'name': 'default'},
    'allow_agent': False,
    'look_for_keys': False
}

def get_yang_modules(device_details):
    # Cria um filtro XML para a operação get
    filter_xml = """
    <filter>
        <netconf-state xmlns="urn:ietf:params:xml:ns:yang:ietf-netconf-monitoring">
            <schemas/>
        </netconf-state>
    </filter>
    """
    with manager.connect(**device_details) as m:
        try:
            # Utiliza o filtro na chamada RPC
            response = m.get(filter=filter_xml)
            # Parse the response and extract schema information
            schemas = []
            xml_data = etree.tostring(response.data_ele, pretty_print=True)
            data_tree = etree.fromstring(xml_data)

            for schema in data_tree.findall('.//{*}schema'):
                schema_info = {
                    'identifier': schema.find('.//{*}identifier').text,
                    'version': schema.find('.//{*}version').text,
                    'format': schema.find('.//{*}format').text,
                }
                schemas.append(schema_info)
            return schemas
        except Exception as e:
            print(f"Erro ao buscar módulos YANG: {e}")
            return None

# Use a função e imprima os resultados
yang_modules = get_yang_modules(device)
if yang_modules:
    for module in yang_modules:
        print(module)
