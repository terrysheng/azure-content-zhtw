<properties 
	pageTitle="Application Analytics 的參考資料" 
	description="Application Analytics 中的規則運算式，適用於 Application Insights 的強大搜尋工具。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="awills"/>

# 參考資料

[Application Analytics](app-analytics.md) 是強大的搜尋引擎，適合用於 [Application Insights](app-insights-overview.md) 遙測。這些頁面說明 Application Analytics 查詢語言 CSL。



[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## 規則運算式



[> 規則運算式的一般說明](https://github.com/google/re2/wiki/Syntax)。

此頁面列出 RE2 接受的規則運算式語法。它也會列出 PCRE、PERL 及 VIM 所接受的語法。

||
|---|---
|單一字元︰ | 
|. |任何字元，可能包含新行字元 (s = true) 
|[xyz] |字元類別 
|[^xyz] |否定字元類別 
|\\d |Perl 字元類別 
|\\D |否定 Perl 字元類別 
|[[:alpha:]] |ASCII 字元類別 
|[[:^alpha:]] |否定 ASCII 字元類別 
|\\pN |Unicode 字元類別 (單字母名稱) 
|\\p{Greek} |Unicode 字元類別 
|\\PN |否定 Unicode 字元類別 (單字母名稱) 
|\\P{Greek} |否定 Unicode 字元類別 
|複合︰ | 
|xy |x 後面接著 y 
|x&#124;y |x 或 y (偏好 x) 
| 
|重複： | 
| |零個或多個 x，偏好多個 
|x+ |一個或多個 x，偏好多個 
|x? |零個或一個 x，偏好一個 
|x{n,m} |n 或 n+1 或 ... 或 m 個 x，偏好多個 
|x{n,} |n 個或多個 x，偏好多個 
|x{n} |剛好 n 個 x 
|x*? |零個或多個 x，偏好較少個 
|x+? |一個或多個 x，偏好較少個 
|x?? |零個或一個 x，偏好零 
|x{n,m}? |n 或 n+1 或 ... 或 m 個 x，偏好較少個 
|x{n,}? |n 個或多個 x，偏好較少個 
|x{n}? |剛好 n 個 x 
|x{} |(== x*) (不支援) VIM 
|x{-} |(== x*?) (不支援) VIM 
|x{-n} |(== x{n}?) (不支援) VIM 
|x= |(== x?) (不支援) VIM 
|實作限制︰計數表單 x{n,m}、x{n,} 和 x{n} | 
|會拒絕可建立超過 1000 之最小或最大重複計數的表單。 | 
|無限制的重複則不受限於這項限制。 | 
|佔有重複： | 
|x*+ |零個或多個 x，佔有 (不支援) 
|x++ |一個或多個 x，佔有 (不支援) 
|x?+ |零個或一個 x，佔有 (不支援) 
|x{n,m}+ |n 或 ... 或 m 個 x，佔有 (不支援) 
|x{n,}+ |n 個或多個 x，佔有 (不支援) 
|x{n}+ |剛好 n 個 x，佔有 (不支援) 
|群組： | 
|(re) |編號擷取群組 (子相符項目) 
|(?P<name>re) |具名且編號的擷取群組 (子相符項目) 
|(?<name>re) |具名且編號的擷取群組 (子相符項目) (不支援) 
|(?'name're) |具名且編號的擷取群組 (子相符項目) (不支援) 
|(?:re) |非擷取群組 
|(?flags) |在目前的群組內設定旗標；非擷取 
|(?flags:re) |在 re 期間設定旗標；非擷取 
|(?#text) |註解 (不支援) 
|(?&#124;x&#124;y&#124;z) |分支編號重設 (不支援) 
|(?>re) |re 的佔有相符項目 (不支援) 
|re@> |re 的佔有相符項目 (不支援) VIM 
|%(re) |非擷取群組 (不支援) VIM 
|旗標： | 
|i |不區分大小寫 (預設為 false) 
|m |多行模式：^ 和 $ 會比對開始行/結束行以及開始文字/結束文字 (預設為 false) 
|s |讓 . 比對 \\n (預設為 false) 
|U |取消窮盡︰交換 x* 和 x*?、x+ 和 x+? 等的意義 (預設為 false) 
|旗標語法為 xyz (set) 或 -xyz (clear) 或 xy-z (set xy, clear z)。
| 
|空字串： | 
|^ |在文字或行的開頭 (m=true) 
|$ |在文字 (例如 \\z 而非 \\Z) 或行的結尾 (m=true) 
|\\A |在文字的開頭 
|\\b |在 ASCII 文字邊界 (一邊是 \\w 另一邊是 \\W、\\A 或 \\z) 
|\\B |不在 ASCII 文字邊界 
|\\G |在所搜尋的子文字開頭 (不支援) PCRE 
|\\G |在最後一個相符項目結尾 (不支援) PERL 
|\\Z |在文字的結尾，或文字結尾的新行字元之前 (不支援) 
|\\z |在文字的結尾 
|(?=re) |在符合 re 的文字之前 (不支援) 
|(?!re) |在不符合 re 的文字之前 (不支援) 
|(?<=re) |在符合 re 的文字之後 (不支援) 
|(?<!re) |在不符合 re 的文字之後 (不支援) 
|re& |在符合 re 的文字之前 (不支援) VIM 
|re@= |在符合 re 的文字之前 (不支援) VIM 
|re@! |在不符合 re 的文字之前 (不支援) VIM 
|re@<= |在符合 re 的文字之後 (不支援) VIM 
|re@<! |在不符合 re 的文字之後 (不支援) VIM 
|\\zs |設定相符項目的的開頭 (= \\K) (不支援) VIM 
|\\ze |設定相符項目的的結尾 (不支援) VIM 
|\\%^ |檔案開頭 (不支援) VIM 
|\\%$ |檔案結尾 (不支援) VIM 
|\\%V |在畫面上 (不支援) VIM 
|\\%# |游標位置 (不支援) VIM 
|\\%'m |標示 m 位置 (不支援) VIM 
|\\%23l |在行 23 (不支援) VIM 
|\\%23c |在欄 23 (不支援) VIM 
|\\%23v |在虛擬欄 23 (不支援) VIM 
|逸出序列： | 
|\\a |bell (== \\007) 
|\\f |form feed (== \\014) 
|\\t |水平 tab (== \\011) 
|\\n |新行字元 (== \\012) 
|\\r |歸位字元 (== \\015) 
|\\v |重值 tab 字元 (== \\013) 
|\* |常值 \*，針對任何標點符號字元 \* 
|\\123 |八進位字元代碼 (最多三位數) 
|\\x7F |十六進位字元代碼 (剛好兩位數) 
|\\x{10FFFF} |十六進位字元代碼 
|\\C |比對單一位元組，即使為 UTF-8 模式 
|\\Q...\\E |常值文字 ... 即使 ... 有標點符號 
|\\1 |反向參考 (不支援) 
|\\b |退格鍵 (不支援) (使用 \\010) 
|\\cK |控制字元 ^K (不支援) (使用 \\001 等) 
|\\e |逸出 (不支援) (使用 \\033) 
|\\g1 |反向參考 (不支援) 
|\\g{1} |反向參考 (不支援) 
|\\g{+1} |反向參考 (不支援) 
|\\g{-1} |反向參考 (不支援) 
|\\g{name} |具名反向參考 (不支援) 
|\\g<name> |子常式呼叫 (不支援) 
|\\g'name' |子常式呼叫 (不支援) 
|\\k<name> |具名反向參考 (不支援) 
|\\k'name' |具名反向參考 (不支援) 
|\\lX |小寫 X (不支援) 
|\\ux |大寫 x (不支援) 
|\\L...\\E |小寫文字 ...(不支援) 
|\\K |重設 $0 的開頭 (不支援) 
|\\N{name} |具名 Unicode 字元 (不支援) 
|\\R |分行符號 (不支援) 
|\\U...\\E |大寫文字 ...(不支援) 
|\\X |延伸 Unicode 序列 (不支援) 
|\\%d123 |十進位字元 123 (不支援) VIM 
|\\%xFF |十六進位字元 FF (不支援) VIM 
|\\%o123 |八進位字元 123 (不支援) VIM 
|\\%u1234 |Unicode 字元 0x1234 (不支援) VIM 
|\\%U12345678 |Unicode 字元 0x12345678 (不支援) VIM 
|字元類別項目： | 
|x |單一字元 
|A-Z |字元範圍 (含) 
|\\d |Perl 字元類別 
|[:foo:] |ASCII 字元類別 foo 
|\\p{Foo} |Unicode 字元類別 Foo 
|\\pF |Unicode 字元類別 F (單字母名稱) 
|具名字元類別做為字元類別項目： | 
|[\\d] |數字 (== \\d) 
|[^\\d] |非數字 (== \\D) 
|[\\D] |非數字 (== \\D) 
|[^\\D] |非非數字 (== \\d) 
|[[:name:]] |字元類別內的具名 ASCII 類別 (== [:name:]) 
|[^[:name:]] |否定字元類別內的具名 ASCII 類別 (== [:^name:]) 
|[\\p{Name}] |字元類別內的具名 Unicode 屬性 (== \\p{Name}) 
|[^\\p{Name}] |否定字元類別內的具名 Unicode 屬性 (== \\P{Name}) 
|Perl 字元類別 (全都僅限 ASCII)： | 
|\\d |數字 (== [0-9]) 
|\\D |非數字 (== [^0-9])
|\\s |空白字元 (== [\\t\\n\\f\\r ]) 
|\\S |非空白字元 (== [^\\t\\n\\f\\r ]) 
|\\w |文字字元 (== [0-9A-Za-z\_]) 
|\\W |非文字字元 (== [^0-9A-Za-z\_]) 
|\\h |水平間距 (不支援) 
|\\H |非水平間距 (不支援) 
|\\v |垂直間距 (不支援) 
|\\V |非垂直間距 (不支援) 
|ASCII 字元類別： | 
|[[:alnum:]] |英數字元 (== [0-9A-Za-z]) 
|[[:alpha:]] |字母 (== [A-Za-z]) 
|[[:ascii:]] |ASCII (== [\\x00-\\x7F]) 
|[[:blank:]] |空白 (== [\\t ]) 
|[[:cntrl:]] |控制項 (== [\\x00-\\x1F\\x7F]) 
|[[:digit:]] |數字 (== [0-9]) 
|[[:graph:]] |圖形 (== [!-~] == [A-Za-z0-9!"#$%&'()*+,-./:;<=>?@[\\]^\_`{&#124;}~]) 
|[[:lower:]] |lower case (== [a-z]) 
|[[:print:]] |printable (== [ -~] == [ [:graph:]]) 
|[[:punct:]] |punctuation (== [!-/:-@[-`{-~]) 
|[[:space:]] |空白字元 (== [\\t\\n\\v\\f\\r ]) 
|[[:upper:]] |大寫 (== [A-Z]) 
|[[:word:]] |文字字元 (== [0-9A-Za-z\_]) 
|[[:xdigit:]] |十六進位數字 (== [0-9A-Fa-f]) 
|Unicode 字元類別名稱--一般類別： | 
|C |其他 
|Cc |控制項 
|Cf |格式 
|Cn |未指派的字碼指標 (不支援) 
|Co |私人使用 
|Cs |替代 
|L |字母 
|LC |大小寫字母 (不支援) 
|L& |大小寫字母 (不支援) 
|Ll |小寫字母 
|Lm |修飾詞字母 
|Lo |其他字母 
|Lt |字首大寫字母 
|Lu |大寫字母 
|M |標記 
|Mc |間距標記 
|Me |括住標記 
|Mn |非間距標記 
|N |數字 
|Nd |小數數字 
|Nl |字母數字 
|No |其他數字 
|P |標點符號 
|Pc |連接器標點符號 
|Pd |虛線標點符號 
|Pe |關閉標點符號 
|Pf |最終標點符號 
|Pi |初始標點符號 
|Po |其他標點符號 
|Ps |開啟標點符號 
|S |符號 
|Sc |貨幣符號 
|Sk |修飾詞符號 
|Sm |數學符號 
|So |其他符號 
|Z |分隔符號 
|Zl |行分隔符號 
|Zp |段落分隔符號 
|Zs |空格分隔符號 
|Unicode 字元類別名稱--字集： | 
|Arabic |阿拉伯文 
|Armenian |亞美尼亞文 
|Balinese |峇里文 
|Bamum |巴姆文 
|Batak |巴塔克文 
|Bengali |孟加拉文 
|Bopomofo |注音符號 
|Brahmi |婆羅米文 
|Braille |點字 
|Buginese |布吉斯文 
|Buhid |布錫語 
|Canadian\_Aboriginal |加拿大原住民統一語音節 
|Carian |卡里亞文 
|Chakma |查克馬文 
|Cham |查姆文 
|Cherokee |卻洛奇文 
|Common |非某一字集特有的字元 
|Coptic |科普特文 
|Cuneiform |楔形文字 
|Cypriot |賽普勒斯文 
|Cyrillic |斯拉夫文 
|Deseret |德瑟列特文 
|Devanagari |梵文字母 
|Egyptian\_Hieroglyphs |埃及象形文字 
|Ethiopic |衣索比亞文 
|Georgian |喬治亞文 
|Glagolitic |格來哥利蒂克語 
|Gothic |歌德文 
|Greek |希臘文 
|Gujarati |古吉拉特文 
|Gurmukhi |果魯穆奇文 
|Han |漢字 
|Hangul |韓文 
|Hanunoo |漢奴勞族文 
|Hebrew |希伯來文 
|Hiragana |平假名 
|Imperial\_Aramaic |帝國阿拉米文 
|Inherited |從上一個字元繼承字集 
|Inscriptional\_Pahlavi |碑銘體巴勒維文 
|Inscriptional\_Parthian |碑銘體帕提亞文 
|Javanese |爪哇文 
|Kaithi |凱提體文 
|Kannada |坎那達文 
|Katakana |片假名 
|Kayah\_Li |開亞里文 
|Kharoshthi |加羅文
|Khmer |高棉文 
|Lao |寮文 
|Latin |拉丁文 
|Lepcha |雷布查文 
|Limbu |林布文 
|Linear\_B |線性 B 
|Lycian |呂西亞文 
|Lydian |利底亞文 
|Malayalam |馬來亞拉姆文 
|Mandaic |曼底克文 
|Meetei\_Mayek |曼尼普爾文 
|Meroitic\_Cursive |梅羅伊草書 
|Meroitic\_Hieroglyphs |梅羅伊象形文字 
|Miao |苗文 
|Mongolian |蒙古文 
|Myanmar |緬甸 
|New\_Tai\_Lue |新傣仂文 (又稱簡體傣仂文) 
|Nko |Nko 
|Ogham |歐甘文 
|Ol\_Chiki |桑塔爾文 
|Old\_Italic |古義大利文 
|Old\_Persian |古波斯文 
|Old\_South\_Arabian |舊南阿拉伯文 
|Old\_Turkic |舊突厥文 
|Oriya |歐利亞文 
|Osmanya |歐斯馬雅文 
|Phags\_Pa |八思巴文 
|Phoenician |腓尼基文 
|Rejang |拉讓文 
|Runic |盧恩文 
|Saurashtra |紹拉斯徹文 
|Sharada |沙拉達文 
|Shavian |蕭伯納文 
|Sinhala |錫蘭文 
|Sora\_Sompeng |索拉僧平文 
|Sundanese |巽丹文 
|Syloti\_Nagri |賽若提納利文
|Syriac |敘利亞文 
|Tagalog |塔加拉族文 
|Tagbanwa |塔班瓦語 
|Tai\_Le |傣哪文 
|Tai\_Tham |大譚文 
|Tai\_Viet |越南傣文 
|Takri |塔其瑞文 
|Tamil |坦米爾文 
|Telugu |特拉古文 
|Thaana |塔安那文 
|Thai |泰文 
|Tibetan |藏文 
|Tifinagh |提弗納文 
|Ugaritic |烏加里特文 
|Vai |范文 
|Yi |爨文 
|Vim 字元類別： | 
|\\i |識別碼字元 (不支援) VIM 
|\\I |\\i 數字除外 (不支援) VIM 
|\\k |關鍵字字元 (不支援) VIM 
|\\K |\\k 數字除外 (不支援) VIM 
|\\f |檔案名稱字元 (不支援) VIM 
|\\F |\\f 數字除外 (不支援) VIM 
|\\p |可列印字元 (不支援) VIM 
|\\P |\\p 數字除外 (不支援) VIM
|\\s |空白字元 (== [ \\t]) (不支援) VIM 
|\\S |非空白字元 (== [^ \\t]) (不支援) VIM 
|\\d |數字 (== [0-9]) VIM 
|\\D |非 \\d VIM 
|\\x |十六進位數字 (== [0-9A-Fa-f]) (不支援) VIM 
|\\X |非 \\x (不支援) VIM 
|\\o |八進位數字 (== [0-7]) (不支援) VIM 
|\\O |非 \\o (不支援) VIM 
|\\w |文字字元 VIM 
|\\W |非 \\w VIM 
|\\h |字頭字元 (不支援) VIM 
|\\H |非 \\h (不支援) VIM 
|\\a |字母 (不支援) VIM 
|\\A |非 \\a (不支援) VIM 
|\\l |小寫 (不支援) VIM 
|\\L |非小寫 (不支援) VIM 
|\\u |大寫 (不支援) VIM 
|\\U |非大寫 (不支援) VIM 
|\_x |\\x 加上新行字元，針對任何 x (不支援) VIM 
|Vim 旗標： | 
|\\c |忽略大小寫 (不支援) VIM 
|\\C |大小寫須相符 (不支援) VIM 
|\\m |magic (不支援) VIM 
|\\M |nomagic (不支援) VIM 
|\\v |verymagic (不支援) VIM 
|\\V |verynomagic (不支援) VIM 
|\\Z |忽略 Unicode 結合字元中的差異 (不支援) VIM 
|魔術： | 
|(?{code}) |任意 Perl 程式碼 (不支援) PERL 
|(??{code}) |延後任意 Perl 程式碼 (不支援) PERL
|(?n) |regexp 擷取群組 n 的遞迴呼叫 (不支援) 
|(?+n) |相關群組 +n 的遞迴呼叫 (不支援) 
|(?-n) |相關群組 -n 的遞迴呼叫 (不支援) 
|(?C) |PCRE 圖說文字 (不支援) PCRE 
|(?R) |整體 regexp 的遞迴呼叫 (== (?0)) (不支援) 
|(?&name) |具名群組的遞迴呼叫 (不支援) 
|(?P=name) |具名反向參考 (不支援) 
|(?P>name) |具名群組的遞迴呼叫 (不支援) 
|(?(cond)true&#124;false) |條件式分支 (不支援) 
|(?(cond)true) |條件式分支 (不支援) 
|(*ACCEPT) |讓 regexp 更像 Prolog (不支援) 
|(*COMMIT) |(不支援) 
|(*F) |(不支援) 
|(*FAIL) |(不支援) 
|(*MARK) |(不支援) 
|(*PRUNE) |(不支援) 
|(*SKIP) |(不支援)
|(*THEN) |(不支援) 
|(*ANY) |設定新行字元慣例 (不支援) 
|(*ANYCRLF) |(不支援) 
|(*CR) |(不支援) 
|(*CRLF) |(不支援) 
|(*LF) |(不支援) 
|(*BSR\_ANYCRLF) |設定 \\R 慣例 (不支援) PCRE 
|(*BSR\_UNICODE) |(不支援) PCRE




[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->