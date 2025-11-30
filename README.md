# Summative-assessment-analyticse
This is a repositiory used for my summative assessment analytics

from ortools.constraint_solver import pywrapcp, routing_enums_pb2
import math

# Locations (coordinates)
locations = [
    (51.731490, 19.569041),
    (51.680291, 19.572892),
    (51.740260, 19.452051),
    (51.820117, 19.292778),
    (51.804424, 19.211583),
    (51.855164, 19.413300)
]

n = len(locations)

# Parameters
vehicle_speed_kmh = 80
max_leg_distance = 100

# Haversine distance function
def haversine(a, b):
    R = 6371
    lat1, lon1 = a
    lat2, lon2 = b

    dlat = math.radians(lat2 - lat1)
    dlon = math.radians(lon2 - lon1)

    lat1 = math.radians(lat1)
    lat2 = math.radians(lat2)

    h = (
        math.sin(dlat / 2)**2 +
        math.cos(lat1) * math.cos(lat2) *
        math.sin(dlon / 2)**2
    )

    return 2 * R * math.asin(math.sqrt(h))
    
