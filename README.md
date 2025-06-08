import streamlit as st
from youtube_transcript_api import YouTubeTranscriptApi
from pytube import YouTube
from collections import Counter
import re

def extrair_texto_short(url):
    try:
        video_id = extrair_video_id(url)
        transcript = YouTubeTranscriptApi.get_transcript(video_id, languages=['pt', 'en'])
        texto = ' '.join([x['text'] for x in transcript])
    except Exception:
        yt = YouTube(url)
        texto = yt.title  # fallback
    return texto

def extrair_video_id(url):
    if "shorts/" in url:
        return url.split("shorts/")[1].split("?")[0]
    elif "v=" in url:
        return url.split("v=")[1].split("&")[0]
    return url[-11:]

def palavras_mais_comuns(texto, top_n=10):
    palavras = re.findall(r'\b\w+\b', texto.lower())
    return Counter(palavras).most_common(top_n)

st.title("🧠 Analisador de Shorts do YouTube")
url = st.text_input("Cole o link de um vídeo Short do YouTube:")

if url:
    with st.spinner("Extraindo conteúdo do vídeo..."):
        texto = extrair_texto_short(url)
        st.write("🔍 Texto extraído do vídeo:")
        st.write(texto)

        st.subheader("📊 Palavras mais frequentes:")
        palavras = palavras_mais_comuns(texto)
        for palavra, freq in palavras:
            st.write(f"• {palavra}: {freq}x")
