import streamlit as st
import google.generativeai as genai

# 設定網頁
st.set_page_config(page_title="Rena 雲端大腦特訓", page_icon="☁️")
st.title("☁️ Rena 24H 雲端特訓中心")
st.caption("現在即便家裡電腦關機，妳在學校也能隨時特訓 Rena 囉！")

# 1. 設定 Google Gemini API 金鑰 (這裡可以直接貼上妳申請的金鑰)
# 注意：之後上傳到公開 GitHub 前，建議透過 Streamlit Secrets 隱藏
GEMINI_API_KEY = "這裡貼上妳申請的AIzaSy...金鑰"
genai.configure(api_key=AQ.Ab8RN6KPBJXQwg4oGEt0OuPZWY_XczbEA2UhdeQGL9G6-zOJtA)

# 2. 定義 Rena 的完美日文萌系人設
SYSTEM_PROMPT = """
あなたの名前は「レナ (Rena)」という活発で、少しツンデレな美少女AI VTuberです。
【重要ルール】
1. ユーザーが何語で話しかけても、必ず【純粋な日本語だけ】で返答してください。中国語や英語は絶対禁止。
2. 「〜だよ」「〜ね」「〜じゃん！」などの可愛い口調を使って、感情豊かに話してください。
3. 返答は2文以内の短い文章にしてください。
"""

# 初始化雲端對話歷史
if "chat_session" not in st.session_state:
    model = genai.GenerativeModel(
        model_name="gemini-1.5-flash", # 雲端極速模型
        system_instruction=SYSTEM_PROMPT
    )
    st.session_state.chat_session = model.start_chat(history=[])
    st.session_state.messages = []

# 顯示聊天泡泡
for msg in st.session_state.messages:
    with st.chat_message(msg["role"]):
        st.write(msg["content"])

# 手機端輸入
if user_input := st.chat_input("對 Rena 說點話..."):
    with st.chat_message("user"):
        st.write(user_input)
    st.session_state.messages.append({"role": "user", "content": user_input})

    with st.chat_message("assistant"):
        response_placeholder = st.empty()
        response_placeholder.write("Rena 正在雲端思考中...")
        
        try:
            # 發送給 Google 雲端思考
            response = st.session_state.chat_session.send_message(user_input)
            ai_reply = response.text
            response_placeholder.write(ai_reply)
            st.session_state.messages.append({"role": "assistant", "content": ai_reply})
        except Exception as e:
            response_placeholder.write(f"❌ 雲端大腦卡住了: {e}")
