#multi-agent system- financial advisor
##step1- install packages
!pip install -q google-generativeai
## step2 - import libraries
import os
from datetime import datetime
import google.generativeai as genai
from google.colab import userdata
## step3 - access api_key
genai.configure(api_key=userdata.get("test"))
## step4 - load llm
model=genai.GenerativeModel("gemini-2.5-flash",generation_config={"temperature": 0.3})
####

#Agent 1- finacial risk agent

def risk_agent (user_input):
  print("[Risk analysis]")
  prompt=f"""
  You are a financial analyst. Based on the user input analyse the financial situation of the user and classify the risk(low, medium and high).
  Also list top financial concerns in 2-3 lines.
  user input: {user_input}
  """
  response=model.generate_content(prompt)
  return response.text
  print(response.text)

## agent2 - investment advisor agent
def investment_advisor(risk_summary):
  print("[Investment advisory analysis]")
  prompt=f"""
  You are a investment advisor. Based on the financial risk summary, suggest top 3 investment strategies.
  Keep the output confine in 2-3 line in bullet points.
  Financial risk summary:{risk_summary}
  """
  response=model.generate_content(prompt)
  return response.text
  print(response.text)

## agent3 - planning agents
def planning_agent(investment_plan):
  print("[Planning agent]")
  prompt=f"""
  You are a investment planner. Based on the financial risk and investment advise what follow-up questions should financial advisor ask to get more details.
  Keep the output confine in 2-3 line in bullet points.
  planning agent:{investment_plan}
  """
  response=model.generate_content(prompt)
  return response.text
  print(response.text)


## agent 4- logger agent

def logger_agent(name,cid,input_text,summary,plan, followup):
  print("[Logger agent]")
  log_entry=f"""
  time:{datetime.now},
  customer id:{cid},
  customer name:{name},
  input text:{user_input},
  summary:{summary},
  plan:{plan},
  followup:{followup}
  """
  return log_entry
  print(log_entry)
  with open("logger.txt","a") as f:
    f.write(logger_entry)

# User input
name=input("Enter your name: ")
cid=input("Enter your customer id: ")
user_input=input("Enter your question:")


risk_summary= risk_agent(user_input)

investment_plan=investment_advisor(risk_summary)

followup=planning_agent(investment_plan)

logger_agent(name,cid,user_input,risk_summary,investment_plan,followup)
