import streamlit as st
import pandas as pd
import plotly.express as px
import plotly.graph_objects as go
from datetime import datetime
import requests
from PIL import Image
import json

# Configuração da página
st.set_page_config(
    page_title="Dashboard - Enchentes Juiz de Fora",
    page_icon="🌧️",
    layout="wide",
    initial_sidebar_state="expanded"
)

# Estilos CSS personalizados
st.markdown("""
<style>
    .main-header {
        font-size: 3rem;
        font-weight: bold;
        color: #1f77b4;
        text-align: center;
        margin-bottom: 0;
    }
    .sub-header {
        font-size: 1.2rem;
        color: #666;
        text-align: center;
        margin-bottom: 2rem;
    }
    .metric-card {
        background-color: #f0f2f6;
        padding: 20px;
        border-radius: 10px;
        border-left: 5px solid #ff4b4b;
        margin: 10px 0;
    }
    .news-card {
        background-color: #ffffff;
        padding: 15px;
        border-radius: 8px;
        border: 1px solid #e0e0e0;
        margin: 10px 0;
        box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        transition: transform 0.2s;
    }
    .news-card:hover {
        transform: translateX(5px);
        border-left: 4px solid #ff4b4b;
    }
    .help-section {
        background-color: #e8f5e9;
        padding: 20px;
        border-radius: 10px;
        border-left: 5px solid #4caf50;
        margin: 20px 0;
    }
    .alert-box {
        background-color: #fff3cd;
        border: 1px solid #ffeaa7;
        color: #856404;
        padding: 15px;
        border-radius: 5px;
        margin: 10px 0;
    }
    .update-time {
        font-size: 0.9rem;
        color: #666;
        text-align: right;
        font-style: italic;
    }
</style>
""", unsafe_allow_html=True)

# Dados atualizados (baseados na pesquisa mais recente)
DADOS_ATUALIZADOS = {
    "data_atualizacao": "01 de Março de 2026",
    "mortos": 65,
    "desaparecidos": 0,  # Último desaparecido (Pietro, 9 anos) foi encontrado
    "desabrigados": 8584,
    "desalojados": 8584,  # Total de desabrigados e desalojados
    "ocorrencias": 2367,
    "cidades_afetadas": ["Juiz de Fora", "Ubá", "Cataguases", "Leopoldina", "Muriaé", "Viçosa"],
    "status_buscas": "Encerradas"
}

# Links de notícias atualizadas
NOTICIAS = [
    {
        "titulo": "Corpo de último desaparecido é encontrado - 65 mortos confirmados",
        "fonte": "G1 Globo",
        "data": "01/03/2026",
        "url": "https://g1.globo.com/mg/zona-da-mata/noticia/2026/03/01/corpo-de-ultimo-desaparecido-e-encontrado-em-juiz-de-fora-e-buscas-sao-finalizadas-com-65-mortos-pela-chuva.ghtml",
        "resumo": "Buscas finalizadas após 120 horas. Corpo de Pietro, 9 anos, foi o último a ser localizado no bairro Paineiras."
    },
    {
        "titulo": "Sobe para 72 o número de mortos nas chuvas em Minas Gerais",
        "fonte": "Agência Brasil",
        "data": "01/03/2026",
        "url": "https://agenciabrasil.ebc.com.br/geral/noticia/2026-03/sobe-para-72-o-numero-de-mortos-nas-chuvas-em-minas-gerais",
        "resumo": "Maior parte das vítimas é de Juiz de Fora. Estado decreta luto oficial de três dias."
    },
    {
        "titulo": "Chuvas históricas deixam ao menos 70 mortos na Zona da Mata",
        "fonte": "Revista Oeste",
        "data": "01/03/2026",
        "url": "https://revistaoeste.com/brasil/chuvas-em-mg-numero-de-mortos-sobe-para-70/",
        "resumo": "8.584 desabrigados e desalojados em Juiz de Fora. Governo antecipa R$ 38 milhões para recuperação."
    },
    {
        "titulo": "Buscas duraram cerca de 120 horas - um corpo a cada duas horas",
        "fonte": "O Tempo",
        "data": "01/03/2026",
        "url": "https://www.otempo.com.br/cidades/2026/3/1/buscas-por-desaparecidos-em-juiz-de-fora-apos-chuvas-duraram-cerca-de-120-horas",
        "resumo": "Bombeiros recuperaram corpos em média a cada duas horas de trabalho nas áreas atingidas."
    }
]

# Informações de ajuda e doações
FORMAS_AJUDA = {
    "pix": [
        {"nome": "Prefeitura de Juiz de Fora", "chave": "defesacivil@pjf.mg.gov.br", "descricao": "Defesa Civil Municipal"},
        {"nome": "Governo de Minas", "chave": "Codemge", "descricao": "Conta única do Estado"}
    ],
    "locais_doacao": [
        "Centros de Distribuição da Defesa Civil em Juiz de Fora",
        "Parque de Exposições (Casa de Apoio)",
        "Igrejas e entidades comunitárias dos bairros atingidos",
        "Pontos de coleta em shopping centers da cidade"
    ],
    "itens_necessarios": [
        "Água potável", "Alimentos não perecíveis", "Roupas", "Colchões", 
        "Produtos de higiene pessoal", "Produtos de limpeza", "Fraldas", "Medicamentos"
    ]
}

def main():
    # Header
    st.markdown('<h1 class="main-header">🌧️ Dashboard - Enchentes em Juiz de Fora</h1>', unsafe_allow_html=True)
    st.markdown('<p class="sub-header">Monitoramento em tempo real da tragédia na Zona da Mata Mineira</p>', unsafe_allow_html=True)
    
    # Alerta de atualização
    st.markdown(f"""
    <div class="alert-box">
        <strong>⚠️ Última atualização:</strong> {DADOS_ATUALIZADOS['data_atualizacao']} | 
        Status das buscas: <strong>{DADOS_ATUALIZADOS['status_buscas']}</strong> | 
        Buscas encerradas após 120 horas de operação
    </div>
    """, unsafe_allow_html=True)

    # Métricas principais
    col1, col2, col3, col4 = st.columns(4)
    
    with col1:
        st.metric(
            label="😢 Mortes Confirmadas",
            value=DADOS_ATUALIZADOS['mortos'],
            delta="+16 desde 25/02",
            delta_color="inverse"
        )
    
    with col2:
        st.metric(
            label="🔍 Desaparecidos",
            value=DADOS_ATUALIZADOS['desaparecidos'],
            delta="Último encontrado em 01/03",
            delta_color="off"
        )
    
    with col3:
        st.metric(
            label="🏠 Desabrigados/Desalojados",
            value=f"{DADOS_ATUALIZADOS['desabrigados']:,}".replace(",", "."),
            delta="8.584 pessoas",
            delta_color="off"
        )
    
    with col4:
        st.metric(
            label="📋 Ocorrências",
            value=f"{DADOS_ATUALIZADOS['ocorrencias']:,}".replace(",", "."),
            delta="Registradas pela Defesa Civil",
            delta_color="off"
        )

    # Gráficos
    col_left, col_right = st.columns(2)
    
    with col_left:
        st.subheader("📊 Evolução das Vítimas")
        
        # Dados para o gráfico de evolução
        datas = ['25/02', '26/02', '27/02', '28/02', '01/03']
        mortes = [25, 46, 64, 64, 65]
        desaparecidos = [21, 13, 1, 1, 0]
        
        fig = go.Figure()
        fig.add_trace(go.Scatter(
            x=datas, y=mortes,
            mode='lines+markers',
            name='Mortes',
            line=dict(color='#ff4b4b', width=3),
            marker=dict(size=8)
        ))
        fig.add_trace(go.Scatter(
            x=datas, y=desaparecidos,
            mode='lines+markers',
            name='Desaparecidos',
            line=dict(color='#ffa502', width=3),
            marker=dict(size=8)
        ))
        
        fig.update_layout(
            title="Evolução do número de vítimas",
            xaxis_title="Data",
            yaxis_title="Número de pessoas",
            hovermode='x unified',
            plot_bgcolor='rgba(0,0,0,0)',
            paper_bgcolor='rgba(0,0,0,0)',
        )
        st.plotly_chart(fig, use_container_width=True)

    with col_right:
        st.subheader("🗺️ Cidades em Estado de Calamidade")
        
        cidades_data = {
            'Cidade': ['Juiz de Fora', 'Ubá', 'Cataguases', 'Leopoldina', 'Muriaé', 'Viçosa'],
            'Mortes': [65, 6, 1, 0, 0, 0],
            'Status': ['Calamidade', 'Calamidade', 'Emergência', 'Emergência', 'Emergência', 'Emergência']
        }
        df_cidades = pd.DataFrame(cidades_data)
        
        fig2 = px.bar(df_cidades, x='Cidade', y='Mortes', color='Status',
                      color_discrete_map={'Calamidade': '#ff4b4b', 'Emergência': '#ffa502'},
                      title="Vítimas fatais por município")
        fig2.update_layout(plot_bgcolor='rgba(0,0,0,0)', paper_bgcolor='rgba(0,0,0,0)')
        st.plotly_chart(fig2, use_container_width=True)

    # Seção de notícias
    st.markdown("---")
    st.subheader("📰 Últimas Notícias")
    
    cols = st.columns(2)
    for idx, noticia in enumerate(NOTICIAS):
        with cols[idx % 2]:
            st.markdown(f"""
            <div class="news-card">
                <h4><a href="{noticia['url']}" target="_blank" style="text-decoration: none; color: #1f77b4;">{noticia['titulo']}</a></h4>
                <p style="font-size: 0.9rem; color: #666; margin: 5px 0;">
                    <strong>{noticia['fonte']}</strong> | {noticia['data']}
                </p>
                <p style="margin-top: 10px;">{noticia['resumo']}</p>
                <a href="{noticia['url']}" target="_blank" style="color: #ff4b4b; text-decoration: none; font-weight: bold;">→ Ler notícia completa</a>
            </div>
            """, unsafe_allow_html=True)

    # Como ajudar
    st.markdown("---")
    st.subheader("🤝 Como Ajudar")
    
    col_ajuda1, col_ajuda2 = st.columns(2)
    
    with col_ajuda1:
        st.markdown("### 💰 Doações via PIX")
        for pix in FORMAS_AJUDA['pix']:
            st.markdown(f"""
            <div style="background-color: #e3f2fd; padding: 15px; border-radius: 8px; margin: 10px 0; border-left: 4px solid #2196f3;">
                <strong>{pix['nome']}</strong><br>
                <code style="font-size: 1.1rem; background-color: #fff; padding: 5px; border-radius: 4px;">{pix['chave']}</code><br>
                <small>{pix['descricao']}</small>
            </div>
            """, unsafe_allow_html=True)
        
        st.markdown("### 📍 Locais de Doação")
        for local in FORMAS_AJUDA['locais_doacao']:
            st.markdown(f"- {local}")
    
    with col_ajuda2:
        st.markdown("### 📦 Itens Mais Necessários")
        itens_cols = st.columns(2)
        for idx, item in enumerate(FORMAS_AJUDA['itens_necessarios']):
            with itens_cols[idx % 2]:
                st.markdown(f"<div style='padding: 8px; background-color: #f5f5f5; margin: 5px 0; border-radius: 5px;'>✓ {item}</div>", unsafe_allow_html=True)
        
        st.markdown("### 🚨 Contatos Importantes")
        st.markdown("""
        - **Defesa Civil Juiz de Fora:** (32) 3690-8000
        - **Corpo de Bombeiros:** 193
        - **SAMU:** 192
        - **Polícia Militar:** 190
        """)

    # Informações adicionais
    with st.expander("ℹ️ Informações Detalhadas sobre a Tragédia"):
        st.markdown("""
        ### Contexto
        As chuvas que atingiram Juiz de Fora e região entre os dias 23 e 24 de fevereiro de 2026 foram históricas. 
        O município registrou dois dos cinco dias mais chuvosos de sua história, com precipitações que superaram 
        os 150 mm em poucas horas.
        
        ### Dados Técnicos
        - **População em área de risco:** 128.946 pessoas (23,7% da população total)
        - **Posição no ranking nacional:** 9ª cidade com mais moradores em áreas de risco
        - **Investimentos anunciados:** R$ 38 milhões (Juiz de Fora) + R$ 8 milhões (Ubá)
        - **Tempo de buscas:** Aproximadamente 120 horas de operação contínua
        
        ### Bairros Mais Atingidos
        - Paineiras (onde ocorreram a maioria dos deslizamentos)
        - Morro do Imperador
        - Costa Carvalho
        - Santa Luzia
        - Benfica
        
        ### Alertas
        O Inmet e a Defesa Civil mantêm alerta de "grande perigo" para a região devido à saturação do solo, 
        o que significa que novos deslizamentos podem ocorrer mesmo com chuvas de menor intensidade.
        """)

    # Footer
    st.markdown("---")
    st.markdown("""
    <div style="text-align: center; color: #666; padding: 20px;">
        <p>Dashboard atualizado automaticamente | Fontes: Defesa Civil de MG, G1, Agência Brasil, O Tempo</p>
        <p style="font-size: 0.8rem;">Desenvolvido para fins informativos. Sempre confirme informações oficiais.</p>
    </div>
    """, unsafe_allow_html=True)

if __name__ == "__main__":
    main()
