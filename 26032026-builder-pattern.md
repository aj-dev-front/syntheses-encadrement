# Builder Pattern

Le Builder Pattern est un Design Pattern de création, qui permet de créer et surtout de controler la création des objets dams le projet.

# Example

Ici on remarque que le constructor de FestivalEvent est private, ce qui implique 2 choses :

- personne en dehors du builder ne peut instancier un FestivalEvent
- le builder (ici nommé Builder) DOIT se trouver dans la class FestivalEvent, et donc être static

```
class FestivalEvent {
  private name: string;
  private starHour: number;
  private endHour: number;
  private maxParticipants: number;
  private registeredPArtivcipants = 0;

  private constructor(
    name: string,
    startHour: number,
    endHour: number,
    maxParticipants: number,
  ) {
    this.name = name;
    this.starHour = startHour;
    this.endHour = endHour;
    this.maxParticipants = maxParticipants;
  }

  static Builder = class {
    private attr: EventAttr = {
      name: null,
      startHour: null,
      endHour: null,
      maxParticipants: null,
    };

    setName(name: string) {
      if (!this.attr.name) {
        this.attr.name = name;
      }
      return this;
    }

    setStartHour(startHour: number) {
      if (!this.attr.startHour) {
        this.attr.startHour = startHour;
      }
      return this;
    }

    private endHourHigherThanStartHour(endHour: number): boolean {
      return !this.attr.startHour || this.attr.startHour < endHour;
    }

    setEndtHour(endHour: number) {
      if (!this.attr.endHour && this.endHourHigherThanStartHour(endHour)) {
        this.attr.endHour = endHour;
      }
      return this;
    }

    setMaxParticipants(maxParticipants: number) {
      if (!this.attr.maxParticipants) {
        this.attr.maxParticipants = maxParticipants;
      }
      return this;
    }

    build() {
      const { name, startHour, endHour, maxParticipants } = this.attr;
      if (name && startHour && endHour && maxParticipants) {
        return new FestivalEvent(name, startHour, endHour, maxParticipants);
      }
      return null;
    }
  };
}

type EventAttr = {
  name: string | null;
  startHour: number | null;
  endHour: number | null;
  maxParticipants: number | null;
};

const test = new FestivalEvent.Builder()
  .setName("name")
  .setStartHour(30)
  .setEndtHour(31)
  .setMaxParticipants(43)
  .build();

const error = new FestivalEvent.Builder()
  .setName("name")
  .setStartHour(3)
  .setMaxParticipants(4)
  .build();
```

# Ressources

- [Tuto Builder Pattern](https://codecrux.substack.com/p/mastering-the-builder-design-pattern)
- [Builder Pattern en Typescript](https://dev.to/shahjalalbu/mastering-the-builder-pattern-in-typescript-a-complete-guide-2inl)
