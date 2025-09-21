<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Country Code & Flag Finder</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            padding: 20px;
        }
        
        .container {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.3);
            width: 100%;
            max-width: 600px;
            padding: 30px;
            text-align: center;
        }
        
        h1 {
            color: #1a2a6c;
            margin-bottom: 10px;
            font-size: 2.2rem;
        }
        
        .subtitle {
            color: #555;
            margin-bottom: 30px;
            font-size: 1.1rem;
        }
        
        .input-group {
            display: flex;
            margin-bottom: 20px;
            border-radius: 50px;
            overflow: hidden;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
        }
        
        .input-group input {
            flex: 1;
            padding: 18px 25px;
            border: none;
            font-size: 1.2rem;
            outline: none;
            background: #f8f9fa;
        }
        
        .input-group button {
            background: #1a2a6c;
            color: white;
            border: none;
            padding: 0 30px;
            cursor: pointer;
            font-size: 1.1rem;
            font-weight: 600;
            transition: all 0.3s ease;
        }
        
        .input-group button:hover {
            background: #0d1b4d;
        }
        
        .results-list {
            margin-top: 20px;
            max-height: 450px;
            overflow-y: auto;
            padding-right: 10px;
        }
        
        .result-entry {
            background: #f8f9fa;
            border-radius: 15px;
            padding: 15px 20px;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            box-shadow: 0 5px 10px rgba(0, 0, 0, 0.05);
            animation: slideIn 0.5s ease-out;
        }

        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateY(-20px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }
        
        .country-identifier {
            display: flex;
            align-items: center;
            gap: 15px;
            font-weight: 700;
            color: #1a2a6c;
            font-size: 1.5rem;
        }
        
        .flag-emoji {
            font-size: 2.2rem;
            line-height: 1;
        }

        .copy-btn {
            background-color: #e9ecef;
            color: #495057;
            border: none;
            padding: 8px 15px;
            border-radius: 20px;
            cursor: pointer;
            font-weight: 600;
            transition: background-color 0.3s, color 0.3s;
            display: flex;
            align-items: center;
            gap: 5px;
            flex-shrink: 0;
        }

        .copy-btn:hover {
            background-color: #ced4da;
        }
        
        .copy-btn.copied {
            background-color: #28a745;
            color: white;
        }

        .initial-message {
            color: #777;
            font-size: 1.2rem;
            min-height: 150px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1><i class="fas fa-search-location"></i> Country Code & Flag Finder</h1>
        <p class="subtitle">Enter a phone number with country code to find the country name and flag.</p>
        
        <div class="input-group">
            <input type="text" id="phoneNumberInput" placeholder="Enter phone number e.g., 880171..." autofocus>
            <button id="lookupBtn"><i class="fas fa-search"></i> Finder</button>
        </div>
        
        <div class="results-list" id="resultsList">
            <div class="initial-message" id="initialMessage">Results will be displayed here in a list.</div>
        </div>
    </div>

    <script>
        const countryDatabase = {
            '+1': { name: 'United States', iso2: 'us' },
            '+1242': { name: 'Bahamas', iso2: 'bs' },
            '+1246': { name: 'Barbados', iso2: 'bb' },
            '+1264': { name: 'Anguilla', iso2: 'ai' },
            '+1268': { name: 'Antigua and Barbuda', iso2: 'ag' },
            '+1284': { name: 'British Virgin Islands', iso2: 'vg' },
            '+1340': { name: 'U.S. Virgin Islands', iso2: 'vi' },
            '+1345': { name: 'Cayman Islands', iso2: 'ky' },
            '+1441': { name: 'Bermuda', iso2: 'bm' },
            '+1473': { name: 'Grenada', iso2: 'gd' },
            '+1649': { name: 'Turks and Caicos Islands', iso2: 'tc' },
            '+1664': { name: 'Montserrat', iso2: 'ms' },
            '+1670': { name: 'Northern Mariana Islands', iso2: 'mp' },
            '+1671': { name: 'Guam', iso2: 'gu' },
            '+1684': { name: 'American Samoa', iso2: 'as' },
            '+1758': { name: 'Saint Lucia', iso2: 'lc' },
            '+1767': { name: 'Dominica', iso2: 'dm' },
            '+1784': { name: 'Saint Vincent and the Grenadines', iso2: 'vc' },
            '+1868': { name: 'Trinidad and Tobago', iso2: 'tt' },
            '+1869': { name: 'Saint Kitts and Nevis', iso2: 'kn' },
            '+1876': { name: 'Jamaica', iso2: 'jm' },
            '+20': { name: 'Egypt', iso2: 'eg' },
            '+211': { name: 'South Sudan', iso2: 'ss' },
            '+212': { name: 'Morocco', iso2: 'ma' },
            '+213': { name: 'Algeria', iso2: 'dz' },
            '+216': { name: 'Tunisia', iso2: 'tn' },
            '+218': { name: 'Libya', iso2: 'ly' },
            '+220': { name: 'Gambia', iso2: 'gm' },
            '+221': { name: 'Senegal', iso2: 'sn' },
            '+222': { name: 'Mauritania', iso2: 'mr' },
            '+223': { name: 'Mali', iso2: 'ml' },
            '+224': { name: 'Guinea', iso2: 'gn' },
            '+225': { name: 'Ivory Coast', iso2: 'ci' },
            '+226': { name: 'Burkina Faso', iso2: 'bf' },
            '+227': { name: 'Niger', iso2: 'ne' },
            '+228': { name: 'Togo', iso2: 'tg' },
            '+229': { name: 'Benin', iso2: 'bj' },
            '+230': { name: 'Mauritius', iso2: 'mu' },
            '+231': { name: 'Liberia', iso2: 'lr' },
            '+232': { name: 'Sierra Leone', iso2: 'sl' },
            '+233': { name: 'Ghana', iso2: 'gh' },
            '+234': { name: 'Nigeria', iso2: 'ng' },
            '+235': { name: 'Chad', iso2: 'td' },
            '+236': { name: 'Central African Republic', iso2: 'cf' },
            '+237': { name: 'Cameroon', iso2: 'cm' },
            '+238': { name: 'Cape Verde', iso2: 'cv' },
            '+239': { name: 'Sao Tome and Principe', iso2: 'st' },
            '+240': { name: 'Equatorial Guinea', iso2: 'gq' },
            '+241': { name: 'Gabon', iso2: 'ga' },
            '+242': { name: 'Congo - Brazzaville', iso2: 'cg' },
            '+243': { name: 'Congo - Kinshasa', iso2: 'cd' },
            '+244': { name: 'Angola', iso2: 'ao' },
            '+245': { name: 'Guinea-Bissau', iso2: 'gw' },
            '+248': { name: 'Seychelles', iso2: 'sc' },
            '+249': { name: 'Sudan', iso2: 'sd' },
            '+250': { name: 'Rwanda', iso2: 'rw' },
            '+251': { name: 'Ethiopia', iso2: 'et' },
            '+252': { name: 'Somalia', iso2: 'so' },
            '+253': { name: 'Djibouti', iso2: 'dj' },
            '+254': { name: 'Kenya', iso2: 'ke' },
            '+255': { name: 'Tanzania', iso2: 'tz' },
            '+256': { name: 'Uganda', iso2: 'ug' },
            '+257': { name: 'Burundi', iso2: 'bi' },
            '+258': { name: 'Mozambique', iso2: 'mz' },
            '+260': { name: 'Zambia', iso2: 'zm' },
            '+261': { name: 'Madagascar', iso2: 'mg' },
            '+262': { name: 'Réunion', iso2: 're' },
            '+263': { name: 'Zimbabwe', iso2: 'zw' },
            '+264': { name: 'Namibia', iso2: 'na' },
            '+265': { name: 'Malawi', iso2: 'mw' },
            '+266': { name: 'Lesotho', iso2: 'ls' },
            '+267': { name: 'Botswana', iso2: 'bw' },
            '+268': { name: 'Eswatini', iso2: 'sz' },
            '+269': { name: 'Comoros', iso2: 'km' },
            '+27': { name: 'South Africa', iso2: 'za' },
            '+290': { name: 'Saint Helena', iso2: 'sh' },
            '+291': { name: 'Eritrea', iso2: 'er' },
            '+297': { name: 'Aruba', iso2: 'aw' },
            '+298': { name: 'Faroe Islands', iso2: 'fo' },
            '+299': { name: 'Greenland', iso2: 'gl' },
            '+30': { name: 'Greece', iso2: 'gr' },
            '+31': { name: 'Netherlands', iso2: 'nl' },
            '+32': { name: 'Belgium', iso2: 'be' },
            '+33': { name: 'France', iso2: 'fr' },
            '+34': { name: 'Spain', iso2: 'es' },
            '+350': { name: 'Gibraltar', iso2: 'gi' },
            '+351': { name: 'Portugal', iso2: 'pt' },
            '+352': { name: 'Luxembourg', iso2: 'lu' },
            '+353': { name: 'Ireland', iso2: 'ie' },
            '+354': { name: 'Iceland', iso2: 'is' },
            '+355': { name: 'Albania', iso2: 'al' },
            '+356': { name: 'Malta', iso2: 'mt' },
            '+357': { name: 'Cyprus', iso2: 'cy' },
            '+358': { name: 'Finland', iso2: 'fi' },
            '+359': { name: 'Bulgaria', iso2: 'bg' },
            '+36': { name: 'Hungary', iso2: 'hu' },
            '+370': { name: 'Lithuania', iso2: 'lt' },
            '+371': { name: 'Latvia', iso2: 'lv' },
            '+372': { name: 'Estonia', iso2: 'ee' },
            '+373': { name: 'Moldova', iso2: 'md' },
            '+374': { name: 'Armenia', iso2: 'am' },
            '+375': { name: 'Belarus', iso2: 'by' },
            '+376': { name: 'Andorra', iso2: 'ad' },
            '+377': { name: 'Monaco', iso2: 'mc' },
            '+378': { name: 'San Marino', iso2: 'sm' },
            '+379': { name: 'Vatican City', iso2: 'va' },
            '+380': { name: 'Ukraine', iso2: 'ua' },
            '+381': { name: 'Serbia', iso2: 'rs' },
            '+382': { name: 'Montenegro', iso2: 'me' },
            '+383': { name: 'Kosovo', iso2: 'xk' },
            '+385': { name: 'Croatia', iso2: 'hr' },
            '+386': { name: 'Slovenia', iso2: 'si' },
            '+387': { name: 'Bosnia and Herzegovina', iso2: 'ba' },
            '+389': { name: 'North Macedonia', iso2: 'mk' },
            '+39': { name: 'Italy', iso2: 'it' },
            '+40': { name: 'Romania', iso2: 'ro' },
            '+41': { name: 'Switzerland', iso2: 'ch' },
            '+420': { name: 'Czechia', iso2: 'cz' },
            '+421': { name: 'Slovakia', iso2: 'sk' },
            '+423': { name: 'Liechtenstein', iso2: 'li' },
            '+43': { name: 'Austria', iso2: 'at' },
            '+44': { name: 'United Kingdom', iso2: 'gb' },
            '+45': { name: 'Denmark', iso2: 'dk' },
            '+46': { name: 'Sweden', iso2: 'se' },
            '+47': { name: 'Norway', iso2: 'no' },
            '+48': { name: 'Poland', iso2: 'pl' },
            '+49': { name: 'Germany', iso2: 'de' },
            '+500': { name: 'Falkland Islands', iso2: 'fk' },
            '+501': { name: 'Belize', iso2: 'bz' },
            '+502': { name: 'Guatemala', iso2: 'gt' },
            '+503': { name: 'El Salvador', iso2: 'sv' },
            '+504': { name: 'Honduras', iso2: 'hn' },
            '+505': { name: 'Nicaragua', iso2: 'ni' },
            '+506': { name: 'Costa Rica', iso2: 'cr' },
            '+507': { name: 'Panama', iso2: 'pa' },
            '+508': { name: 'Saint Pierre and Miquelon', iso2: 'pm' },
            '+509': { name: 'Haiti', iso2: 'ht' },
            '+51': { name: 'Peru', iso2: 'pe' },
            '+52': { name: 'Mexico', iso2: 'mx' },
            '+53': { name: 'Cuba', iso2: 'cu' },
            '+54': { name: 'Argentina', iso2: 'ar' },
            '+55': { name: 'Brazil', iso2: 'br' },
            '+56': { name: 'Chile', iso2: 'cl' },
            '+57': { name: 'Colombia', iso2: 'co' },
            '+58': { name: 'Venezuela', iso2: 've' },
            '+590': { name: 'Guadeloupe', iso2: 'gp' },
            '+591': { name: 'Bolivia', iso2: 'bo' },
            '+592': { name: 'Guyana', iso2: 'gy' },
            '+593': { name: 'Ecuador', iso2: 'ec' },
            '+594': { name: 'French Guiana', iso2: 'gf' },
            '+595': { name: 'Paraguay', iso2: 'py' },
            '+596': { name: 'Martinique', iso2: 'mq' },
            '+597': { name: 'Suriname', iso2: 'sr' },
            '+598': { name: 'Uruguay', iso2: 'uy' },
            '+599': { name: 'Curaçao', iso2: 'cw' },
            '+60': { name: 'Malaysia', iso2: 'my' },
            '+61': { name: 'Australia', iso2: 'au' },
            '+62': { name: 'Indonesia', iso2: 'id' },
            '+63': { name: 'Philippines', iso2: 'ph' },
            '+64': { name: 'New Zealand', iso2: 'nz' },
            '+65': { name: 'Singapore', iso2: 'sg' },
            '+66': { name: 'Thailand', iso2: 'th' },
            '+670': { name: 'Timor-Leste', iso2: 'tl' },
            '+672': { name: 'Norfolk Island', iso2: 'nf' },
            '+673': { name: 'Brunei', iso2: 'bn' },
            '+674': { name: 'Nauru', iso2: 'nr' },
            '+675': { name: 'Papua New Guinea', iso2: 'pg' },
            '+676': { name: 'Tonga', iso2: 'to' },
            '+677': { name: 'Solomon Islands', iso2: 'sb' },
            '+678': { name: 'Vanuatu', iso2: 'vu' },
            '+679': { name: 'Fiji', iso2: 'fj' },
            '+680': { name: 'Palau', iso2: 'pw' },
            '+681': { name: 'Wallis and Futuna', iso2: 'wf' },
            '+682': { name: 'Cook Islands', iso2: 'ck' },
            '+683': { name: 'Niue', iso2: 'nu' },
            '+685': { name: 'Samoa', iso2: 'ws' },
            '+686': { name: 'Kiribati', iso2: 'ki' },
            '+687': { name: 'New Caledonia', iso2: 'nc' },
            '+688': { name: 'Tuvalu', iso2: 'tv' },
            '+689': { name: 'French Polynesia', iso2: 'pf' },
            '+690': { name: 'Tokelau', iso2: 'tk' },
            '+691': { name: 'Micronesia', iso2: 'fm' },
            '+692': { name: 'Marshall Islands', iso2: 'mh' },
            '+7': { name: 'Russia', iso2: 'ru' },
            '+81': { name: 'Japan', iso2: 'jp' },
            '+82': { name: 'South Korea', iso2: 'kr' },
            '+84': { name: 'Vietnam', iso2: 'vn' },
            '+850': { name: 'North Korea', iso2: 'kp' },
            '+852': { name: 'Hong Kong', iso2: 'hk' },
            '+853': { name: 'Macau', iso2: 'mo' },
            '+855': { name: 'Cambodia', iso2: 'kh' },
            '+856': { name: 'Laos', iso2: 'la' },
            '+86': { name: 'China', iso2: 'cn' },
            '+880': { name: 'Bangladesh', iso2: 'bd' },
            '+886': { name: 'Taiwan', iso2: 'tw' },
            '+90': { name: 'Turkey', iso2: 'tr' },
            '+91': { name: 'India', iso2: 'in' },
            '+92': { name: 'Pakistan', iso2: 'pk' },
            '+93': { name: 'Afghanistan', iso2: 'af' },
            '+94': { name: 'Sri Lanka', iso2: 'lk' },
            '+95': { name: 'Myanmar', iso2: 'mm' },
            '+960': { name: 'Maldives', iso2: 'mv' },
            '+961': { name: 'Lebanon', iso2: 'lb' },
            '+962': { name: 'Jordan', iso2: 'jo' },
            '+963': { name: 'Syria', iso2: 'sy' },
            '+964': { name: 'Iraq', iso2: 'iq' },
            '+965': { name: 'Kuwait', iso2: 'kw' },
            '+966': { name: 'Saudi Arabia', iso2: 'sa' },
            '+967': { name: 'Yemen', iso2: 'ye' },
            '+968': { name: 'Oman', iso2: 'om' },
            '+970': { name: 'Palestine', iso2: 'ps' },
            '+971': { name: 'United Arab Emirates', iso2: 'ae' },
            '+972': { name: 'Israel', iso2: 'il' },
            '+973': { name: 'Bahrain', iso2: 'bh' },
            '+974': { name: 'Qatar', iso2: 'qa' },
            '+975': { name: 'Bhutan', iso2: 'bt' },
            '+976': { name: 'Mongolia', iso2: 'mn' },
            '+977': { name: 'Nepal', iso2: 'np' },
            '+98': { name: 'Iran', iso2: 'ir' },
            '+992': { name: 'Tajikistan', iso2: 'tj' },
            '+993': { name: 'Turkmenistan', iso2: 'tm' },
            '+994': { name: 'Azerbaijan', iso2: 'az' },
            '+995': { name: 'Georgia', iso2: 'ge' },
            '+996': { name: 'Kyrgyzstan', iso2: 'kg' },
            '+998': { name: 'Uzbekistan', iso2: 'uz' }
        };

        const phoneNumberInput = document.getElementById('phoneNumberInput');
        const lookupBtn = document.getElementById('lookupBtn');
        const resultsList = document.getElementById('resultsList');
        const initialMessage = document.getElementById('initialMessage');
        
        const sortedCountryCodes = Object.keys(countryDatabase).sort((a, b) => b.length - a.length);

        function isoToEmoji(iso) {
            return iso.toUpperCase().split('').map(char => 
                String.fromCodePoint(char.charCodeAt(0) + 0x1F1A5)
            ).join('');
        }

        function copyToClipboard(text, buttonElement) {
            if (navigator.clipboard && navigator.clipboard.writeText) {
                navigator.clipboard.writeText(text).then(() => {
                    showCopySuccess(buttonElement);
                }).catch(err => {
                    console.warn('Modern copy failed, trying fallback.', err);
                    fallbackCopy(text, buttonElement);
                });
            } else {
                fallbackCopy(text, buttonElement);
            }
        }

        function fallbackCopy(text, buttonElement) {
            const textArea = document.createElement("textarea");
            textArea.value = text;
            textArea.style.position = "fixed";
            textArea.style.left = "-9999px";
            document.body.appendChild(textArea);
            textArea.focus();
            textArea.select();
            try {
                document.execCommand('copy');
                showCopySuccess(buttonElement);
            } catch (err) {
                console.error("Fallback copy failed", err);
            }
            document.body.removeChild(textArea);
        }

        function showCopySuccess(buttonElement) {
            const btnSpan = buttonElement.querySelector('span');
            const btnIcon = buttonElement.querySelector('i');
            
            buttonElement.classList.add('copied');
            if(btnIcon) btnIcon.className = 'fas fa-check';
            if(btnSpan) btnSpan.textContent = 'Copied!';
            
            setTimeout(() => {
                buttonElement.classList.remove('copied');
                if(btnIcon) btnIcon.className = 'fas fa-copy';
                if(btnSpan) btnSpan.textContent = buttonElement.dataset.copyText.startsWith('Input:') ? 'Copy Input' : 'Copy';
            }, 2000);
        }

        function lookupCountry() {
            let number = phoneNumberInput.value.trim();
            
            if (number === '') {
                alert('Please enter a phone number.');
                return;
            }
            
            // Automatically add '+' if it's missing
            if (!number.startsWith('+')) {
                number = '+' + number;
            }

            let foundCountryCode = null;
            for (const code of sortedCountryCodes) {
                if (number.startsWith(code)) {
                    foundCountryCode = code;
                    break;
                }
            }
            
            if (initialMessage) {
                initialMessage.style.display = 'none';
            }

            const country = countryDatabase[foundCountryCode];
            const resultEntry = document.createElement('div');
            resultEntry.className = 'result-entry';
            
            let htmlContent, copyText;

            if (country) {
                const emoji = isoToEmoji(country.iso2);
                copyText = `${emoji} ${country.name}`;
                htmlContent = `
                    <div class="country-identifier">
                        <span class="flag-emoji">${emoji}</span>
                        <span>${country.name}</span>
                    </div>
                    <button class="copy-btn" data-copy-text="${copyText}">
                        <i class="fas fa-copy"></i>
                        <span>Copy</span>
                    </button>
                `;
            } else {
                copyText = `Input: ${number}`;
                htmlContent = `
                    <div class="country-identifier">
                        <span class="flag-emoji">❓</span>
                        <span>Country not found</span>
                    </div>
                    <button class="copy-btn" data-copy-text="${copyText}">
                        <i class="fas fa-copy"></i>
                        <span>Copy Input</span>
                    </button>
                `;
            }
            
            resultEntry.innerHTML = htmlContent;
            resultsList.prepend(resultEntry);
            phoneNumberInput.value = '';
            phoneNumberInput.focus();
        }

        lookupBtn.addEventListener('click', lookupCountry);
        
        phoneNumberInput.addEventListener('keyup', (e) => {
            if (e.key === 'Enter') {
                lookupCountry();
            }
        });

        resultsList.addEventListener('click', function(e) {
            const copyBtn = e.target.closest('.copy-btn');
            if (copyBtn) {
                const textToCopy = copyBtn.dataset.copyText;
                copyToClipboard(textToCopy, copyBtn);
            }
        });

    </script>
</body>
</html>```
