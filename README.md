# 💫 About Me:
<br>I'm a🤖 Passionate BSc AI/ML student at Gujarat University, exploring the frontiers of artificial intelligence and machine learning. I am growing increasingly fond of LLMs lately! 


## 🌐 Socials:
[![Instagram](https://img.shields.io/badge/Instagram-%23E4405F.svg?logo=Instagram&logoColor=white)](https://instagram.com/malav_patel17) [![LinkedIn](https://img.shields.io/badge/LinkedIn-%230077B5.svg?logo=linkedin&logoColor=white)](https://linkedin.com/in/malav-patel-766196302) [![email](https://img.shields.io/badge/Email-D14836?logo=gmail&logoColor=white)](mailto:malav0003@gmail.com) 

# 💻 Tech Stack:
![C](https://img.shields.io/badge/c-%2300599C.svg?style=for-the-badge&logo=c&logoColor=white) ![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white) ![HTML5](https://img.shields.io/badge/lua-%232C2D72.svg?style=for-the-badge&logo=lua&logoColor=white) ![R](https://img.shields.io/badge/r-%23276DC3.svg?style=for-the-badge&logo=r&logoColor=white) ![CSS3](https://img.shields.io/badge/css3-%231572B6.svg?style=for-the-badge&logo=css3&logoColor=white) ![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54) ![Firebase](https://img.shields.io/badge/firebase-%23039BE5.svg?style=for-the-badge&logo=firebase) ![Google Cloud](https://img.shields.io/badge/GoogleCloud-%234285F4.svg?style=for-the-badge&logo=google-cloud&logoColor=white) ![Oracle](https://img.shields.io/badge/Oracle-F80000?style=for-the-badge&logo=oracle&logoColor=white) ![Netlify](https://img.shields.io/badge/netlify-%23000000.svg?style=for-the-badge&logo=netlify&logoColor=#00C7B7) ![MySQL](https://img.shields.io/badge/mysql-4479A1.svg?style=for-the-badge&logo=mysql&logoColor=white) ![Firebase](https://img.shields.io/badge/firebase-a08021?style=for-the-badge&logo=firebase&logoColor=ffcd34) ![Adobe](https://img.shields.io/badge/adobe-%23FF0000.svg?style=for-the-badge&logo=adobe&logoColor=white) ![Blender](https://img.shields.io/badge/blender-%23F5792A.svg?style=for-the-badge&logo=blender&logoColor=white) ![Canva](https://img.shields.io/badge/Canva-%2300C4CC.svg?style=for-the-badge&logo=Canva&logoColor=white) ![Adobe Photoshop](https://img.shields.io/badge/adobe%20photoshop-%2331A8FF.svg?style=for-the-badge&logo=adobe%20photoshop&logoColor=white) ![Adobe After Effects](https://img.shields.io/badge/Adobe%20After%20Effects-9999FF.svg?style=for-the-badge&logo=Adobe%20After%20Effects&logoColor=white) ![Pandas](https://img.shields.io/badge/pandas-%23150458.svg?style=for-the-badge&logo=pandas&logoColor=white) ![NumPy](https://img.shields.io/badge/numpy-%23013243.svg?style=for-the-badge&logo=numpy&logoColor=white) ![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white) ![Git](https://img.shields.io/badge/git-%23F05033.svg?style=for-the-badge&logo=git&logoColor=white)


### ✍️ Random Dev Quote
![](https://quotes-github-readme.vercel.app/api?type=horizontal&theme=radical)
# 📊 GitHub Stats:
![](https://github-readme-stats.shion.dev/api?username=kingspy89&theme=blue_navy&hide_border=false&include_all_commits=true&count_private=true)<br/>
![](https://streak-stats.demolab.com/?user=kingspy89&theme=blue_navy&hide_border=false)<br/>
![](https://github-readme-stats.shion.dev/api/top-langs/?username=kingspy89&theme=blue_navy&hide_border=false&include_all_commits=true&count_private=true&layout=compact)

---
[![](https://komarev.com/ghpvc/?username=kingspy89&icon=0&color=0)](https://visitcount.itsvg.in)

#!/bin/bash

GITHUB_USERNAME="$USERNAME"
GITHUB_TOKEN="$GH_PAT"

get_json_from_graphql() {
  local graphql=$1
  local graphql_variables=$2
  local formatted_graphql=$(echo "$graphql" | tr -d '\n' | sed 's/  */ /g')
  local query='{"query": "'$formatted_graphql'", "variables": '$graphql_variables'}'  
  local json=$(curl -s -H "Authorization: bearer $GITHUB_TOKEN" -X POST -d "$query" https://api.github.com/graphql)
  echo "$json"
}

years=$(bash "AfterReadme/get-contribution-years.sh")
streak=0

for year in $years; do
  from_date="${year}-01-01T00:00:00Z"
  to_date="${year}-12-31T23:59:59Z"

  get_daily_contribs_graphql='
query($username: String!, $from_date: DateTime!, $to_date: DateTime!) {
  user(login: $username) {
    contributionsCollection(from: $from_date, to: $to_date) {
      contributionCalendar{
        weeks {
          contributionDays{
            contributionCount
          }
        }
      }
    }
  }
}'

  json=$(get_json_from_graphql "$get_daily_contribs_graphql" '{"username": "'$GITHUB_USERNAME'", "from_date": "'$from_date'", "to_date": "'$to_date'"}')
  daily_contribs=$(echo "$json" | jq -r '[.data.user.contributionsCollection.contributionCalendar.weeks[].contributionDays[]]')
  reversed_daily_contribs=$(echo "$daily_contribs" | jq 'reverse')

  contrib_counts=($(echo "$reversed_daily_contribs" | grep '"contributionCount"' | sed -E 's/[^0-9]*([0-9]+).*/\1/'))


  # positive streak
  for contrib_count in "${contrib_counts[@]}"; do
    if [ "$contrib_count" -gt 0 ]; then
      streak=$((streak + 1))
    else
      break 2
    fi
  done

  # days since streak reset (negative value)
  if [ "$streak" -eq 0 ]; then
    for contrib_count in "${contrib_counts[@]}"; do
      if [ "$contrib_count" -eq 0 ]; then
        streak=$((streak - 1))
      else
        break 2
      fi
    done
  fi

  # no contribution today but streak is not reset
  if [ "$streak" -eq -1 ]; then
    streak=0
  fi
done

echo $streak
