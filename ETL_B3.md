#import de bibliotecas
import pandas as pd
#criando mascara para txt e automatizando para receber mais dados

def read_files(path, n_file, year_date, type_file):

    _file = f'{path}{n_file}{year_date}.{type_file}'
    
    colspecs =[(2,10), 
              (10,12), 
              (12,24), 
              (27,39),  
              (56,69), 
              (69,82), 
              (82,95), 
              (108,121), 
              (152,170),
              (170,188)]
    names = ['Data_pregao', 'Codbdi', 'Sigla_acao', 'nome_acao', 'Preco_abertura', 'Preco_Maximo', 'Preco_Minimo', 'Preco_Fechamento', 'Quantidade_negociacao', 'Volume_de_negociacao']
    df = pd.read_fwf(_file, colspecs=colspecs, names=names, skiprows =1)
    
    return df
    
    #Filtrar ações
def filter_stocks(df):
    df = df [df['Codbdi']== 2]
    df = df.drop(['Codbdi'], 1)
    
    return df
    
    # ajustes data
def parse_date(df):
    df['Data_pregao'] = pd.to_datetime(df['Data_pregao'], format = '%Y%m%d') #ajustar campo de data
    
    return df
    
    # ajustes numéricos
def parse_values(df):
    df['Preco_abertura'] = (df['Preco_abertura'] /100).astype(float)
    df['Preco_Maximo'] = (df['Preco_Maximo'] /100).astype(float)
    df['Preco_Minimo'] = (df['Preco_Minimo'] /100).astype(float)
    df['Preco_Fechamento'] = (df['Preco_Fechamento'] /100).astype(float)
    
    return df
    
    #junção dos files

def concat_files(path, n_file, year_date, type_file, final_file):
    for i, y in enumerate(year_date):
        df = read_files(path, n_file, y, type_file)
        df = filter_stocks(df)
        df = parse_date(df)
        df = parse_values(df)
        
        if i==0:
            df_final = df
        else:
            df_final = pd.concat([df_final, df]) #concatenar a versão atual com os novos dados
            
    df_final.to_csv(f'{path}//{final_file}', index=False) #salvar base em CSV
    
    #executar script
year_date = ['2018', '2019', '2020']
path=f'D:/Pyhton/txt/'
n_file = 'COTAHIST_A'
type_file = 'txt'
final_file = 'all_bovesp.csv'

concat_files( path, n_file, year_date, type_file, final_file)
 
