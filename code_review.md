One of the issues I may contribute to is a rake task that counts elements in each
yml file so the documentation can reflect how many elements each category of fake
data will generate. This will allow users to know if a category is diverse enough
for their purpose.

Since I am unfamiliar with creating rake tasks, this may be too challenging. I may
also create issues for the project that will suggest adding more faker categories
(for example I would like to add characters and quotes from The Expanse tv show to use for
fake data).

I've started with the test folder. Since a feature I would like to add is data
related to a tv show, here is an example of a test in that category:

```
class TestFakerTvShowsNewGirl < Test::Unit::TestCase
  def setup
    @tester = Faker::TvShows::NewGirl
  end

  def test_character
    assert @tester.character.match(/\w+/)
  end

  def test_quote
    assert @tester.quote.match(/\w+/)
  end
end
```
This test is representative of most test files. It is straight forward, simply
using regular expressions to make sure the output matches any word character
(per `\w+`). My test for The Expanse category will look virtually the same as
this.

Now let's move to the YAML files. Here is the file corresponding to the New Girl
category:

```
en:
  faker:
    new_girl:
      characters: [
        "Winston Bishop",
        "Cece",
        "Coach",
        "Jessica Day",
        "Nick Miller",
        "Schmidt"
        ]
      quotes: [
        "A no-nail oath? You thought I was gonna sleep with one of you, like I just couldn't help it?",
        "With your big, beautiful blue eyes and my Blair Underwood-like skin, we'd have the most beautiful baby the world has ever seen.",
        "Are you 'White Fanging' me?",
        "You are a beautiful white man, Nick Miller.",
        "Take these, and take care of them. I can get a tetanus shot, but I can't cure damaged suede.",
        "Please take that off. You look like a homeless pencil.",
        "Winston and Ferguson about to eat some pasta!",
        "Gave me cookie got you cookie!",
        "Can you believe the zoo wouldn't let me borrow their white tiger? I mean, the nerve! Philip Seymour Hoffman is going to be sitting at the back of the party thinking, 'Look at that guy. He couldn’t even get a big cat.'",
        "Did you just say 'butt drinking?' You can’t say 'butt drinking' and not explain what it is. That's two of my four favorite things.",
        "Are you cooking a frittata in a sauce pan? What is this – prison?",
        "If you had a jacket on with a bunch of yellow feathers, I'd be done.",
        "I had figure skating lessons until I was 13, and then my mom sobered up and realized I was a boy.",
        "Damp towel, damp! It’s like a really big wet nap. I feel like I'm being licked by a golden retriever. Look at this bathroom. There should not be two girls in this bathroom. You're too humid.",
        "I don't celebrate Christmas. Or, as I like to call it, 'White Anglo Saxon Winter Privilege Night'.",
        "It hurts. I feel like I've laid a million eggs and they're all hatching. I feel like I want to murder someone and I also want soft pretzels.",
        "I don't know how it happened, man, but somehow I got on that woman's cycle of menstruation…and I got that menstruation inside of me.",
        "I don't want some janky freshwater bitch fish, Winston. I want a lionfish.",
        "I did laundry. I smell like a baby in a damn meadow.",
        "Old people freak me out. With their hands and their legs. They’re like the people version of pleated pants.",
        "I'm really fighting the urge to buy you a lobster dinner.",
        "Nick is delicate. Like a flower. Like a chubby, damaged flower who hates himself.",
        "How's the new apartment? Does it smell like new paint and compromise?",
        "I was sabotaged by my baby box, which means I will never trust anything that comes out of it.",
        "I'm gonna end up alone. I’m gonna be a single old lady, flashing people on the subway.",
        "This place is fancy and I don't know which fork to kill myself with.",
        "If you need me, I'll be in my room, listening to some mainstream hip-hop."
      ]
      ```
This file holds the data to be referenced for the category. I would write a similar
file for my implementation of The Expanse category. Perhaps I could add another
set for ships in addition to quotes and characters.

We now move on to the ruby class associated with New Girl:

```
module Faker
  module TvShows
    class NewGirl < Base
      flexible :new_girl

      class << self
        def character
          fetch('new_girl.characters')
        end

        def quote
          fetch('new_girl.quotes')
        end
      end
    end
  end
end
```

This class inherits from the `Base` class and mixes in code from two modules,
`Faker` and `TvShows` (`TvShows` simply inherits from Faker and provides no additional
functionality).

I do not understand what `flexible :new_girl` does. Cursory searches for an answer
have come up short. I'll keep looking. If I can't find anything, I'll reach out
to other developers working on Faker for an answer. I assume it is associated
with the fetch method nested in the class methods since `new_girl` is referenced
in both places.

There are two class methods, each corresponding to the type of data retrieved
from the respective YAML file. Each employ the `fetch` method which is inherited from
the `Base` class:
```
# Helper for the common approach of grabbing a translation
# with an array of values and selecting one of them.

def fetch(key)
  fetched = sample(translate("faker.#{key}"))
  if fetched&.match(%r{^\/}) && fetched&.match(%r{\/$}) # A regex
    regexify(fetched)
  else
    fetched
  end
end
```

There is A LOT going on in both the `Base` class and `Faker` module (faker/lib/faker.rb)
This is clearly the gem's "main file".

I know enough about the code to add new categories such as The Expanse, so I can move forward with
creating the issue and proposing the solution.

I plan on going over the main file with my mentor so I can better understand the
details of how this code works.
