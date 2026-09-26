# Catálogo de features

Material complementar do TCC "Estabilidade da seleção de atributos como sinal de concept drift na detecção de phishing", do MBA em Data Science e Analytics da USP/Esalq, de Leonardo Takashi Teramatsu. Detalha o Apêndice A do TCC. Versão 1 em 25 de Setembro de 2026.

## 1. Espaço de features

| Conjunto | Definição 1 | Definição 2 | O que entra na Definição 2 |
|---|---|---|---|
| d₀ | 87 | 87 | as 87 features do Dataset A (Hannousse e Yahiouche, 2021) |
| d₁ | 87 | 95 | as oito da Rodada 01 |
| d₂ | 87 | 95 | nenhuma: a Rodada 02 reavaliou as oito da Rodada 01 com mais dados |
| d₃ | 87 | 103 | as oito da Rodada 03 |
| d₄ | 87 | 114 | as 11 da Rodada 04 |
| d₅ | 87 | 123 | as nove da Rodada 05 |

d₀ reúne o treino e a validação do Dataset A, e dᵢ acrescenta a ele os lotes lote₁ a loteᵢ do Dataset B. As 36 novas features foram propostas com apoio de uma ferramenta de IA generativa (Anthropic, 2024) e implementadas no codigo de extração de features, uma função por feature, com o mesmo nome.

Convenções das tabelas da seção 3:

- ε = 10⁻⁹, e ln é o logaritmo natural;
- nomes entre crases são features do Dataset A usadas no cálculo;
- ‡ marca a direção contraintuitiva, com valores maiores nas URLs legítimas;
- quando o cálculo falha, a função devolve −1, a mesma marca do extrator original.

## 2. As 87 features de partida

São as features de Hannousse e Yahiouche (2021), calculadas a partir da cadeia da URL, do conteúdo da página (HTML e DOM) e de serviços externos. Estão em cinco grupos:

- **Estruturais e lexicais da URL (39).** Calculadas só a partir da cadeia de caracteres da URL, com contagens e razões de caracteres, marcadores estruturais e indicadores de ofuscação: length_url, length_hostname, ip, nb_dots, nb_hyphens, nb_at, nb_qm, nb_and, nb_or, nb_eq, nb_underscore, nb_tilde, nb_percent, nb_slash, nb_star, nb_colon, nb_comma, nb_semicolumn, nb_dollar, nb_space, nb_www, nb_com, nb_dslash, http_in_path, https_token, ratio_digits_url, ratio_digits_host, punycode, port, tld_in_path, tld_in_subdomain, abnormal_subdomain, nb_subdomains, prefix_suffix, random_domain, shortening_service, path_extension, nb_redirection, nb_external_redirection.
- **Estatísticas de palavras e tokens (11).** Comprimentos mínimo, máximo e médio de palavras na URL, no host e no caminho (path), além da repetição de caracteres: length_words_raw, char_repeat, shortest_words_raw, shortest_word_host, shortest_word_path, longest_words_raw, longest_word_host, longest_word_path, avg_words_raw, avg_word_host, avg_word_path.
- **Marca, indícios e reputação textual (6).** Sinais de personificação de marca e listas de termos suspeitos: phish_hints, domain_in_brand, brand_in_subdomain, brand_in_path, suspecious_tld, statistical_report.
- **Conteúdo da página (24).** Extraídas do conteúdo renderizado, com estrutura de hyperlinks, redirecionamentos, mídia, formulários e elementos de interface: nb_hyperlinks, ratio_intHyperlinks, ratio_extHyperlinks, ratio_nullHyperlinks, nb_extCSS, ratio_intRedirection, ratio_extRedirection, ratio_intErrors, ratio_extErrors, login_form, external_favicon, links_in_tags, submit_email, ratio_intMedia, ratio_extMedia, sfh, iframe, popup_window, safe_anchor, onmouseover, right_clic, empty_title, domain_in_title, domain_with_copyright.
- **Serviços externos e reputação (7).** Obtidas de fontes de terceiros (WHOIS, tráfego, DNS e indexação): whois_registered_domain, domain_registration_length, domain_age, web_traffic, dns_record, google_index, page_rank.

## 3. As 36 features novas

### Rodada 01: entram em d₁

| Feature | O que mede | Cálculo | Obra |
|---|---|---|---|
| index_dns_conflict | incoerência entre indexação no Google e registro DNS ativo | `google_index` XOR `dns_record`: 1 quando só um dos dois está presente | Mohammad et al. (2014) |
| subdomain_entropy | aleatoriedade do subdomínio | entropia de Shannon (base 2) dos caracteres do subdomínio, isto é, dos rótulos do host antes dos dois últimos; 0 sem subdomínio | Marchal et al. (2014) |
| url_phish_kw_count | termos de coleta de credenciais na URL | quantos dos 28 termos da lista aparecem na URL em minúsculas | Ma et al. (2009) |
| digit_ratio_path | dígitos no caminho, típicos de tokens e identificadores de sessão | dígitos do caminho ÷ comprimento do caminho; 0 com caminho vazio | Xiang et al. (2011) |
| hyphen_density_subdomain | hífens do encadeamento de palavras-chave no subdomínio | hífens ÷ comprimento do primeiro rótulo do host, quando o host tem mais de dois rótulos; 0 nos demais casos | Blum et al. (2010) |
| free_hosting_indicator | uso de plataforma de hospedagem gratuita | 1 se o host contém um dos 21 domínios da lista de plataformas; 0 caso contrário | Khonji et al. (2013) |
| upper_ratio_path | maiúsculas no caminho | maiúsculas ÷ letras do caminho; 0 sem letras | Le et al. (2018) |
| tld_risk_score | risco do domínio de topo (TLD) pela taxa de abuso | nível de 1 a 3 para os 22 TLDs de uma tabela; 0 para os demais | Agten et al. (2015) |

### Rodada 03: entram em d₃

| Feature | O que mede | Cálculo | Obra |
|---|---|---|---|
| url_entropy_full | aleatoriedade da URL completa | entropia de Shannon (base 2) dos caracteres da URL em minúsculas | Sahingoz et al. (2019) |
| redirection_asymmetry ‡ | redirecionamentos externos em relação aos internos | `ratio_extRedirection` ÷ (`ratio_intRedirection` + ε), limitado a 200 | Mohammad et al. (2014) |
| abs_ext_hyperlinks ‡ | número absoluto de hyperlinks externos | `nb_hyperlinks` × `ratio_extHyperlinks` | Mohammad et al. (2014) |
| vowel_ratio_hostname ‡ | proporção de vogais no host | vogais ÷ caracteres do host sem "www." e sem pontos | Hannousse e Yahiouche (2021) |
| ratio_ext_to_int_hyperlinks | hyperlinks externos em relação aos internos | `ratio_extHyperlinks` ÷ (`ratio_intHyperlinks` + ε), limitado a 100 | Hannousse e Yahiouche (2021) |
| word_ratio_host_path ‡ | comprimento médio das palavras do host em relação ao das palavras do caminho | `avg_word_host` ÷ (`avg_word_path` + ε), limitado a 50 | Sahingoz et al. (2019) |
| char_diversity_hostname ‡ | variedade de caracteres no host | caracteres distintos ÷ total de caracteres alfanuméricos do host | Bilge et al. (2011) |
| path_has_extension | extensão de arquivo no último segmento do caminho | 1 se o último segmento termina em ponto seguido de até cinco caracteres alfanuméricos; 0 caso contrário | Garera et al. (2007) |

### Rodada 04: entram em d₄

| Feature | O que mede | Cálculo | Obra |
|---|---|---|---|
| longest_word_path_ratio_url | peso da maior palavra do caminho no comprimento da URL | `longest_word_path` ÷ (`length_url` + ε) | Meenu e Godara (2019) |
| max_run_same_char_url ‡ | repetição contígua de caracteres | maior sequência de caracteres idênticos consecutivos na URL | Rao et al. (2019) |
| ratio_int_of_total_links ‡ | participação dos links internos no total | `ratio_intHyperlinks` ÷ (`ratio_intHyperlinks` + `ratio_extHyperlinks` + ε) | Abdelhamid et al. (2014) |
| ip_x_inv_log_age | host formado por endereço IP, ponderado pela juventude do domínio | `ip` × 1 ÷ (ln(1 + max(`domain_age`, 0)) + 1) | Aljofey et al. (2022) |
| digit_letter_transitions_host | alternância entre dígitos e letras no host, indício de domínios gerados por algoritmo (DGA) | transições entre dígito e letra ÷ (n − 1), sobre os n caracteres alfanuméricos do host; 0 se n < 2 | Al-Duwairi et al. (2020) |
| links_tags_x_ext_ratio | links em tags ponderados pela proporção de links externos | `links_in_tags` × `ratio_extHyperlinks` | Pan e Ding (2006) |
| digit_block_count_host | blocos numéricos no host | número de sequências contíguas de dígitos no host | Bilge et al. (2011) |
| shortest_to_avg_word_ratio | menor palavra em relação ao comprimento médio das palavras | `shortest_words_raw` ÷ (`avg_words_raw` + ε) | Pan e Ding (2006) |
| nb_dots_path | extensões e dupla extensão no caminho | número de pontos no caminho | Garera et al. (2007) |
| slash_density ‡ | densidade de barras na URL | `nb_slash` ÷ (`length_url` + ε) | Livadas et al. (2006) |
| null_ratio_per_log_total_links | links nulos ponderados pelo total de links | \|`ratio_nullHyperlinks`\| ÷ (ln(1 + max(`nb_hyperlinks`, 0)) + 1) | Mohammad et al. (2012) |

### Rodada 05: entram em d₅

| Feature | O que mede | Cálculo | Obra |
|---|---|---|---|
| host_entropy | aleatoriedade do host completo, indício de DGA | entropia de Shannon (base 2) dos caracteres do host | Antonakakis et al. (2011) |
| log_age_registration_ratio ‡ | idade do domínio em relação ao período de registro | ln(1 + max(`domain_age`, 0) ÷ max(`domain_registration_length`, 1)) | Xiang et al. (2011) |
| has_hex_token_path | token hexadecimal no caminho | 1 se algum segmento do caminho é hexadecimal puro, com oito ou mais caracteres; 0 caso contrário | Kim et al. (2022) |
| log_ext_per_int_links | links externos em relação aos internos, em escala logarítmica | ln(1 + `ratio_extHyperlinks` ÷ (`ratio_intHyperlinks` + ε)) | Mohammad et al. (2012) |
| vowel_ratio_host ‡ | proporção de vogais entre as letras do host | vogais ÷ letras do host, com "www" | Bilge et al. (2011) |
| alpha_ratio_reg_domain ‡ | letras no domínio registrado | letras ÷ comprimento do penúltimo rótulo do host; em sufixos compostos, como .com.br, esse rótulo não é o domínio registrado | Mohammad et al. (2015) |
| nb_special_chars_query | caracteres fora do padrão na query string | caracteres da query que não são letras, dígitos nem = & - _ . | Sahoo et al. (2017) |
| traffic_x_page_rank ‡ | reputação conjunta de tráfego e ranking | `web_traffic` × `page_rank` | Basnet et al. (2008) |
| log_age_x_dns ‡ | idade do domínio com registro DNS ativo | ln(1 + max(`domain_age`, 0)) × `dns_record` | Xiang et al. (2011) |

vowel_ratio_hostname (Rodada 03) e vowel_ratio_host (Rodada 05) medem quase o mesmo sinal: a primeira divide as vogais por todos os caracteres do host sem "www." e sem pontos; a segunda, só pelas letras do host inteiro.

## 4. Implementadas, mas fora do experimento

`new_features.py` também implementa cinco features de uma Rodada 06: log_abs_ext_hyperlinks, log_ext_per_int_redirect, extcss_x_inv_log_age, avg_segment_length_path e nb_numeric_segments. O experimento do TCC termina em d₅ e não as usa.

## 5. Referências

Abdelhamid, N.; Ayesh, A.; Thabtah, F. 2014. Phishing detection based associative classification data mining. Expert Systems with Applications 41(13): 5948-5959.

Agten, P.; Joosen, W.; Piessens, F.; Nikiforakis, N. 2015. Seven months' worth of mistakes: a longitudinal study of typosquatting abuse. In: Network and Distributed System Security Symposium, 2015, San Diego, CA, EUA.

Al-Duwairi, B.; Jarrah, M.H.; Shatnawi, A.S. 2020. PASSVM: a highly accurate online fast flux detection system. Disponível em: <https://arxiv.org/abs/2006.03566>. Acesso em: 25 de Maio de 2026.

Aljofey, A.; Jiang, Q.; Rasool, A.; Chen, H.; Liu, W.; Qu, Q.; Wang, Y. 2022. An effective detection approach for phishing websites using URL and HTML features. Scientific Reports 12: 8842.

Anthropic. 2024. The Claude 3 Model Family: Opus, Sonnet, Haiku. Disponível em: <https://api.semanticscholar.org/CorpusID:268232499>. Acesso em: 25 de Maio de 2026.

Antonakakis, M.; Perdisci, R.; Lee, W.; Vasiloglou II, N.; Dagon, D. 2011. Detecting malware domains at the upper DNS hierarchy. In: USENIX Security Symposium, 2011, San Francisco, CA, EUA.

Basnet, R.; Mukkamala, S.; Sung, A.H. 2008. Detection of phishing attacks: a machine learning approach. p. 373-383. In: Prasad, B. Soft Computing Applications in Industry. Springer, Berlin, Alemanha.

Bilge, L.; Kirda, E.; Kruegel, C.; Balduzzi, M. 2011. EXPOSURE: finding malicious domains using passive DNS analysis. In: Network and Distributed System Security Symposium, 2011, San Diego, CA, EUA.

Blum, A.; Wardman, B.; Solorio, T.; Warner, G. 2010. Lexical feature based phishing URL detection using online learning. In: ACM Workshop on Artificial Intelligence and Security, 2010, Chicago, IL, EUA. p. 54-60.

Garera, S.; Provos, N.; Chew, M.; Rubin, A.D. 2007. A framework for detection and measurement of phishing attacks. In: ACM Workshop on Recurring Malcode, 2007, Alexandria, VA, EUA. p. 1-8.

Hannousse, A.; Yahiouche, S. 2021. Towards benchmark datasets for machine learning based website phishing detection: an experimental study. Engineering Applications of Artificial Intelligence 104: 104347.

Khonji, M.; Iraqi, Y.; Jones, A. 2013. Phishing detection: a literature survey. IEEE Communications Surveys & Tutorials 15(4): 2091-2121.

Kim, T.; Park, N.; Hong, J.; Kim, S.W. 2022. Phishing URL detection: a network-based approach robust to evasion. In: ACM SIGSAC Conference on Computer and Communications Security, 2022, Los Angeles, CA, EUA. p. 1769-1782.

Le, H.; Pham, Q.; Sahoo, D.; Hoi, S.C.H. 2018. URLNet: learning a URL representation with deep learning for malicious URL detection. Disponível em: <https://arxiv.org/abs/1802.03162>. Acesso em: 25 de Maio de 2026.

Livadas, C.; Walsh, R.; Lapsley, D.; Strayer, W.T. 2006. Using machine learning techniques to identify botnet traffic. In: IEEE LCN Workshop on Network Security, 2006, Tampa, FL, EUA. p. 967-974.

Ma, J.; Saul, L.K.; Savage, S.; Voelker, G.M. 2009. Beyond blacklists: learning to detect malicious web sites from suspicious URLs. In: ACM SIGKDD International Conference on Knowledge Discovery and Data Mining, 2009, Paris, França. p. 1245-1254.

Marchal, S.; François, J.; State, R.; Engel, T. 2014. PhishStorm: detecting phishing with streaming analytics. IEEE Transactions on Network and Service Management 11(4): 458-471.

Meenu; Godara, S. 2019. Phishing detection using machine learning techniques. International Journal of Engineering and Advanced Technology 9(2): 3820-3829.

Mohammad, R.M.; McCluskey, T.L.; Thabtah, F. 2012. An assessment of features related to phishing websites using an automated technique. In: International Conference for Internet Technology and Secured Transactions, 2012, London, Reino Unido. p. 492-497.

Mohammad, R.M.; Thabtah, F.; McCluskey, L. 2014. Intelligent rule-based phishing websites classification. IET Information Security 8(3): 153-160.

Mohammad, R.M.; Thabtah, F.; McCluskey, L. 2015. Phishing Websites Features. School of Computing and Engineering, University of Huddersfield, Huddersfield, Reino Unido.

Pan, Y.; Ding, X. 2006. Anomaly based web phishing page detection. In: Annual Computer Security Applications Conference, 2006, Miami Beach, FL, EUA. p. 381-392.

Rao, R.S.; Vaishnavi, T.; Pais, A.R. 2019. CatchPhish: detection of phishing websites by inspecting URLs. Journal of Ambient Intelligence and Humanized Computing 11: 813-825.

Sahingoz, O.K.; Buber, E.; Demir, O.; Diri, B. 2019. Machine learning based phishing detection from URLs. Expert Systems with Applications 117: 345-357.

Sahoo, D.; Liu, C.; Hoi, S.C.H. 2017. Malicious URL detection using machine learning: a survey. Disponível em: <https://arxiv.org/abs/1701.07179>. Acesso em: 25 de Maio de 2026.

Xiang, G.; Hong, J.; Rose, C.P.; Cranor, L. 2011. CANTINA+: a feature-rich machine learning framework for detecting phishing web sites. ACM Transactions on Information and System Security 14(2): 21:1-21:28.
