# Airbnb-Clone #13

- #13.0 Template. Form, Url, Setup
- #13.1 Starting the Form
- #13.2 Select Choices
- #13.3 Amenities and Facilities Form
- #13.4 Finishing the Form
- #13.5 Filtering Like a Boss part One
- #13.6 Filtering Like a Boss part Two
- #13.7 Introduction to Django Forms
- #13.8 I love Django Forms For Ever
- #13.9 Forms are Awesome!
- #13.10 Finishing Up!

---

## > Making the Form

### 1. 

### 2. [getlist](https://docs.djangoproject.com/en/3.0/ref/request-response/#django.http.QueryDict.getlist)



---

## > Searching Like a Boss

### 1. [Field Lookup](https://docs.djangoproject.com/en/3.0/ref/models/querysets/#field-lookups) : Conditional Filtering

- 

```python
def search(request):
    city = request.GET.get("city", "Anywhere")
    city = str.capitalize(city)
    country = request.GET.get("country", "KR")
    room_type = int(request.GET.get("room_type", 0))
    price = int(request.GET.get("price", 0))
    guests = int(request.GET.get("guests", 0))
    bedrooms = int(request.GET.get("bedrooms", 0))
    beds = int(request.GET.get("beds", 0))
    baths = int(request.GET.get("baths", 0))
    instant = bool(request.GET.get("instant", False))
    superhost = bool(request.GET.get("superhost", False))
    room_types = models.RoomType.objects.all()
    amenities = models.Amenity.objects.all()
    facilities = models.Facility.objects.all()
    s_amenities = request.GET.getlist("amenities")
    s_facilities = request.GET.getlist("facilities")

    form = {
        "city": city,
        "s_room_type": room_type,
        "s_country": country,
        "price": price,
        "guests": guests,
        "bedrooms": bedrooms,
        "beds": beds,
        "baths": baths,
        "s_amenities": s_amenities,
        "s_facilities": s_facilities,
        "instant": instant,
        "superhost": superhost,
    }
    choices = {
        "countries": countries,
        "room_types": room_types,
        "amenities": amenities,
        "facilities": facilities,
    }
    filter_args = {}

    if city != "Anywhere":
        filter_args["city__startswith"] = city

    if room_type != 0:
        filter_args["room_type__pk"] = room_type

    if price != 0:
        filter_args["price__lte"] = price

    if guests != 0:
        filter_args["guest__gte"] = guests

    if bedrooms != 0:
        filter_args["bedrooms__gte"] = bedrooms

    if beds != 0:
        filter_args["beds__gte"] = beds

    if baths != 0:
        filter_args["baths__gte"] = baths

    if instant:
        filter_args["instant_book"] = True

    if superhost:
        filter_args["host__superhost"] = True

    filter_args["country"] = country
    rooms = models.Room.objects.filter(**filter_args)

    if len(s_amenities) > 0:
        for s_amenity in s_amenities:
            rooms = rooms.filter(amenities__pk=int(s_amenity))

    if len(s_facilities) > 0:
        for s_facility in s_facilities:
            rooms = rooms.filter(facilities__pk=int(s_facility))

    return render(request, "rooms/search.html", {**form, **choices, "rooms": rooms},)
```

```django
{% extends "base.html" %}

{% block page_title %}
    Search
{% endblock page_title %}

{% block search-bar %}
{% endblock search-bar %}

{% block content %}
    <h2>search!</h2>
    <form method="get" action="{% url 'rooms:search' %}">
        <div>
            <label for="city">City</label>
            <input value="{{city}}" id="city" name="city" placeholder="Search by City" />
        </div>

        <div>
            <label for="country">Country</label>
            <select id="country" name="country">
                {% for country in countries%}
                    <option value="{{country.code}}" {% if country.code == s_country %} selected {% endif %}>{{country.name}}</option>
                {% endfor %}
            </select>
        </div>
        <div>
            <label for="room_type">Room_type</label>
            <select id="room_type" name="room_type">
                <option value="0" {% if s_room_type == 0 %} selected {%endif%}> Any kind </option>
                {% for room_type in room_types%}
                    <option value={{room_type.pk}} {% if s_room_type == room_type.pk %} selected {%endif%}>{{room_type.name}}</option>
                {% endfor %}
            </select>
        </div>
        <div>
            <label for="price">Price</label>
            <input value="{{price}}" type="number" name="price" placeholeder="Price" />
        </div>
        <div>
            <label for="guests">Guests</label>
            <input value="{{guests}}" type="number" name="guests" placeholeder="Guests" />
        </div>
        <div>
            <label for="bedrooms">Bedrooms</label>
            <input value="{{bedrooms}}" type="number" name="bedrooms" placeholeder="Bedrooms" />
        </div>
        <div>
            <label for="beds">Beds</label>
            <input value="{{beds}}" type="number" name="beds" placeholeder="Beds" />
        </div>
        <div>
            <label for="baths">Baths</label>
            <input value="{{baths}}" type="number" name="baths" placeholeder="Baths" />
        </div>
        <div>
            <label for="instant">Instant Book Only?</label>
            <input type="checkbox" name="instant" id="instant"
            {% if instant %}
                checked
            {% endif %}/>
        </div>
        <div>
            <label for="superhost">By Superhost Only?</label>
            <input type="checkbox" name="superhost" id="superhost"
            {% if superhost %}
                checked
            {% endif %}/>
        </div>
        <div>
            <h3>Amenities</h3>
            <ul>
            {% for amenity in amenities %}
                <li>
                    <label for="a_{{amenity.pk}}">{{amenity.name}}</label>
                    <input id="a_{{amenity.pk}}" name="amenities" type="checkbox" value={{amenity.pk}}
                    {% if amenity.pk|slugify in s_amenities %}
                        checked
                    {% endif %}/>
                </li>
            {% endfor %}
            </ul>
        </div>
        <div>
            <h3>Facilities</h3>
            <ul>
            {% for facility in facilities %}
                <li>
                    <label for="f_{{facility.pk}}">{{facility.name}}</label>
                    <input id="f_{{facility.pk}}" name="facilities" type="checkbox" value={{facility.pk}}
                    {% if facility.pk|slugify in s_facilities %}
                        checked
                    {% endif %}/>/>
                </li>
            {% endfor %}
            </ul>
        </div>
        <button>Search</button>
    </form>

    <h3> Results</h3>
    {% for room in rooms %}
        <h3>{{room.name}}</h3>
    {% endfor %}
{% endblock content %}
```

---

## > Create Searching bar Using [Forms API](https://docs.djangoproject.com/en/3.0/ref/forms/api/#module-django.forms)

### 1. Bounded / Unbounded Form