import streamlit as st
import pandas as pd

# Configuração da página
st.set_page_config(page_title="Simulador de Comissão", layout="wide")

st.title("📊 Simulador de Comissão e Metas")
st.markdown("---")

# Definição dos Indicadores baseados nas suas imagens
# O tipo define se o número é inteiro (Positivação) ou decimal (Volume)
indicadores_padrao = [
    {"nome": "VOLUME STILL+ÁGUAS TOTAL CXU", "tipo": "decimal"},
    {"nome": "VOLUME SSD FAMILIAR TOTAL CXU", "tipo": "decimal"},
    {"nome": "POSITIVAÇÃO ALCOÓLICOS S/CERVEJA", "tipo": "inteiro"},
    {"nome": "POSITIVAÇÃO ARTD", "tipo": "inteiro"},
    {"nome": "POSITIVAÇÃO CERVEJA", "tipo": "inteiro"},
    {"nome": "VOLUME SSD SEM AÇÚCAR CXU", "tipo": "decimal"},
    {"nome": "VOLUME SSD INDIVIDUAL TOTAL CXU", "tipo": "decimal"},
    {"nome": "POSITIVAÇÃO PERFETTI", "tipo": "inteiro"},
    {"nome": "POSITIVAÇÃO MONSTER", "tipo": "inteiro"},
    {"nome": "POSITIVAÇÃO TOTAL", "tipo": "inteiro"},
]

# Barra lateral para configurações globais
st.sidebar.header("Configurações")
salario_base_comissao = st.sidebar.number_input("Valor Base da Comissão (R$)", value=1000.0, step=100.0)

# Criando a interface de entrada de dados
dados_calculados = []
soma_pesos = 0

col_header1, col_header2, col_header3, col_header4, col_header5 = st.columns([3, 1.5, 1.5, 1.5, 1.5])
with col_header1: st.subheader("Indicador")
with col_header2: st.subheader("Peso (%)")
with col_header3: st.subheader("Meta")
with col_header4: st.subheader("Realizado")
with col_header5: st.subheader("Atingimento")

st.write("---")

for ind in indicadores_padrao:
    c1, c2, c3, c4, c5 = st.columns([3, 1.5, 1.5, 1.5, 1.5])
    
    with c1:
        st.write(f"**{ind['nome']}**")
    
    with c2:
        # Aqui você define quanto esse indicador vale na meta total
        peso = st.number_input(f"Peso %", min_value=0.0, max_value=100.0, value=10.0, key=f"peso_{ind['nome']}", label_visibility="collapsed")
        soma_pesos += peso
        
    with c3:
        # Input de Meta
        if ind['tipo'] == 'decimal':
            meta = st.number_input(f"Meta", value=1000.0, step=0.01, key=f"meta_{ind['nome']}", label_visibility="collapsed")
        else:
            meta = st.number_input(f"Meta", value=10, step=1, key=f"meta_{ind['nome']}", label_visibility="collapsed")
            
    with c4:
        # Input de Realizado
        if ind['tipo'] == 'decimal':
            realizado = st.number_input(f"Realizado", value=0.0, step=0.01, key=f"real_{ind['nome']}", label_visibility="collapsed")
        else:
            realizado = st.number_input(f"Realizado", value=0, step=1, key=f"real_{ind['nome']}", label_visibility="collapsed")
    
    with c5:
        # Cálculo do Atingimento
        if meta > 0:
            atingimento = (realizado / meta) * 100
        else:
            atingimento = 0.0
            
        # Cor do atingimento
        cor = "green" if atingimento >= 100 else "red"
        st.markdown(f"<span style='color:{cor}; font-weight:bold'>{atingimento:.2f}%</span>", unsafe_allow_html=True)
    
    # Adiciona à lista para cálculo final
    pontos_ganhos = (atingimento * peso) / 100
    # Regra de teto: Se a regra da empresa for que atingimento > 100% não gera mais que o peso, descomente a linha abaixo
    # pontos_ganhos = min(pontos_ganhos, peso) 
    
    dados_calculados.append({
        "Indicador": ind['nome'],
        "Peso": peso,
        "Atingimento": atingimento,
        "Contribuição Final": pontos_ganhos
    })

st.write("---")

# Cálculos Finais
atingimento_geral = sum([d['Contribuição Final'] for d in dados_calculados])
comissao_final = salario_base_comissao * (atingimento_geral / 100)

# Exibição dos Resultados
st.header("🏁 Resultado da Simulação")

col1, col2, col3 = st.columns(3)

with col1:
    st.metric("Total de Pesos Distribuídos", f"{soma_pesos:.1f}%", delta=f"{100-soma_pesos:.1f}% restante" if soma_pesos != 100 else "Ok", delta_color="inverse")

with col2:
    st.metric("Atingimento Geral da Meta", f"{atingimento_geral:.2f}%")

with col3:
    st.metric("Comissão Estimada", f"R$ {comissao_final:.2f}")

# Barra de progresso visual
st.progress(min(atingimento_geral / 100, 1.0))

if soma_pesos != 100:
    st.warning(f"⚠️ Atenção: A soma dos pesos dos indicadores está em {soma_pesos}%. Para o cálculo ser exato, deve somar 100%.")
