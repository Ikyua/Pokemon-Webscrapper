from typing import List, NamedTuple
from urllib.request import Request, urlopen

from bs4 import BeautifulSoup

scrapped_pokemon_data = []


class Pokemon(NamedTuple):
    id: int
    name: str
    avatar: str
    details_path: str
    types: List[str]
    total: int
    hp: int
    attack: int
    defense: int
    sp_attack: int
    sp_defense: int
    speed: int
    entry: str


# Going to the website
url = 'https://pokemondb.net/pokedex/all'
request = Request(
    url,
    headers={'User-Agent': 'Mozilla/5.0'}
)
# Reading from the website and turning the bites into a string for us to parse through
page = urlopen(request)
page_content_bytes = page.read()
page_html = page_content_bytes.decode("utf-8")

# Parsing through the website using the html we got and BS4's parser.
soup = BeautifulSoup(page_html, "html.parser")
# Finding a table, going to its body and getting the data we want. In this case the pokemon data.
pokemon_rows = soup.find_all("table", id="pokedex")[0].find_all("tbody")[0].find_all("tr")

pokemonScrapped = 0
# Beginning of Scrapping
for pokemon in pokemon_rows[0:100]:
    pokemon_data = pokemon.find_all("td")
    id = pokemon_data[0]['data-sort-value']
    avatar = pokemon_data[0].find_all("img")[0]['src']

    name = pokemon_data[1].find_all("a")[0].getText()
    if pokemon_data[1].find_all("small"):  # Avoiding printing the same name if there's a mega variation
        name = pokemon_data[1].find_all("small")[0].getText()

    detail_uri = pokemon_data[1].find_all("a")[0]["href"]

    types = []
    for pokemon_types in pokemon_data[2].find_all("a"):
        types.append(pokemon_types.getText())

    total = pokemon_data[3].getText()
    hp = pokemon_data[4].getText()
    attack = pokemon_data[5].getText()
    defense = pokemon_data[6].getText()
    sp_attack = pokemon_data[7].getText()
    sp_defense = pokemon_data[8].getText()
    speed = pokemon_data[9].getText()

    entry_url = f'https://pokemondb.net{detail_uri}'
    request = Request(
        entry_url,
        headers={'User-Agent': 'Mozilla/5.0'}
    )
    entry_page_html = urlopen(request).read().decode("utf-8")
    entry_soup = BeautifulSoup(entry_page_html, "html.parser")

    try:
        entry_text = \
        entry_soup.find_all("main")[0].find_all("div", {"class": "resp-scroll"})[2].find_all("tr")[0].find_all("td")[
            0].getText()
    except:
        print(f"Could not find pokemon entry text of {name}")
        entry_text = ""

    typed_pokemon = Pokemon(
        id=int(id),
        name=name,
        avatar=avatar,
        details_path=detail_uri,
        types=types,
        total=int(total),
        hp=int(hp),
        attack=int(attack),
        defense=int(defense),
        sp_attack=int(sp_attack),
        sp_defense=int(sp_defense),
        speed=int(speed),
        entry=entry_text
    )
    scrapped_pokemon_data.append(typed_pokemon)
    pokemonScrapped += 1

    print(scrapped_pokemon_data)
