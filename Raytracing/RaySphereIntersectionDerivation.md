# 0 - VARIABLE DEFINITIONS
```
- WP : WORLD POSITION
- RO : RAY ORIGIN
- RD : RAY DIRECTION
- ID : INTERSECTION DISTANCE
- SP : SPHERE POSITION
- SR : SPHERE RADIUS
- RP : RELATIVE POSITION
- QC : QUADRATIC COEFFICIENT
- LC : LINEAR COEFFICIENT
- CC : CONSTANT COEFFICIENT
- NC : NUMERIC CONSTANT
```
# 1 - RAY DEFINITION
```
WP = RO + RD * ID;
```
# 2 - SPHERE DEFINITION
```
(WP - SP) · (WP - SP) = (SR)^2.0;
```
# 3 - SPHERE SUBSTITUTION
```
WP = RO + RD * ID;
```
```
(RO + RD * ID - SP) · (RO + RD * ID - SP) = (SR)^2.0;
```
# 4 - RELATIVE VECTOR DEFINITION
```
RP = RO - SP;
```
```
(RP + RD * ID) · (RP + RD * ID) = (SR)^2.0;
```
# 5 - EXPANSION OF THE QUADRATIC FORM
```
(RP · RP) + 2.0 * (RP · RD) * ID + (RD · RD) * (ID)^2.0 = (SR)^2.0;
```
```
(RD · RD) * (ID)^2.0 + 2.0 * (RP · RD) * ID + (RP · RP) - (SR)^2.0 = 0.0;
```
# 6 - COEFFICIENT DEFINITIONS
```
QC = (RD · RD);
LC = 2.0 * (RP · RD);
CC = (RP · RP) - (SR)^2.0;
```
# 7 - QUADRATIC EQUATION DEFINITION
```
QC * (ID)^2.0 + LC * ID + CC = 0.0;
(QC * (ID)^2.0 + LC * ID + CC) / QC = 0.0;
(ID)^2.0 + (LC / QC) * ID + CC / QC = 0.0;
```
# 8 - COMPLETING THE SQUARE
```
(ID + NC)^2.0 = (ID)^2.0 + 2.0 * NC * ID + (NC)^2.0;
```
```
2.0 * NC * ID = (LC / QC) * ID;
2.0 * NC = LC / QC;
NC = LC / (2.0 * QC);
```
```
(ID)^2.0 + 2.0 * NC * ID + CC / QC = 0.0;
(ID)^2.0 + 2.0 * NC * ID = -CC / QC;
(ID + NC)^2.0 = -CC / QC + (NC)^2.0;
```
# 9 - FINAL SOLUTION
```
ID + NC = -(-CC / QC + (NC)^2.0)^0.5;
ID + NC = +(-CC / QC + (NC)^2.0)^0.5;
```
```
ID = -NC - (-CC / QC + (NC)^2.0)^0.5;
ID = -NC + (-CC / QC + (NC)^2.0)^0.5;
```
```
ID = -(LC / (2.0 * QC)) - ((LC / (2.0 * QC))^2.0 - CC / QC)^0.5;
ID = -(LC / (2.0 * QC)) + ((LC / (2.0 * QC))^2.0 - CC / QC)^0.5;
```
```
ID = (-LC - (LC^2.0 - 4.0 * QC * CC)^0.5) / (2.0 * QC);
ID = (-LC + (LC^2.0 - 4.0 * QC * CC)^0.5) / (2.0 * QC);
```
# SOURCE CODE
```
const float UnfoundIntersection = -1.0;

struct source { vec3 position; vec3 direction; };
struct sphere { vec3 position; float radius; };

float getSphereIntersection(const in source sampleSource, const in sphere sampleSphere, const in float minimumIntersection, const in float maximumIntersection)
{
    vec3 relativePosition = sampleSource.position - sampleSphere.position;

    float quadraticCoefficient = dot(sampleSource.direction, sampleSource.direction);
    float linearCoefficient = 2.0 * dot(relativePosition, sampleSource.direction);
    float constantCoefficient = dot(relativePosition, relativePosition) - sampleSphere.radius * sampleSphere.radius;
    float quadraticDiscriminant = linearCoefficient * linearCoefficient - 4.0 * quadraticCoefficient * constantCoefficient;

    if (quadraticDiscriminant < 0.0) return UnfoundIntersection;

    float discriminantRadical = sqrt(quadraticDiscriminant);

    float negativeIntersection = (-linearCoefficient - discriminantRadical) / (2.0 * quadraticCoefficient);
    float positiveIntersection = (-linearCoefficient + discriminantRadical) / (2.0 * quadraticCoefficient);
    float incomingIntersection = min(negativeIntersection, positiveIntersection);
    float outgoingIntersection = max(negativeIntersection, positiveIntersection);

    if (incomingIntersection > minimumIntersection && incomingIntersection < maximumIntersection) return incomingIntersection;
    if (outgoingIntersection > minimumIntersection && outgoingIntersection < maximumIntersection) return outgoingIntersection;

    return UnfoundIntersection;
}

bool checkSphereIntersection(const in source sampleSource, const in sphere sampleSphere, out float sphereIntersection)
{
    const float MinimumIntersection = 0.001;
    const float MaximumIntersection = 1000.0;
    
    sphereIntersection = getSphereIntersection(sampleSource, sampleSphere, MinimumIntersection, MaximumIntersection);
    
    return sphereIntersection >= MinimumIntersection && sphereIntersection <= MaximumIntersection;
}

void mainImage(out vec4 fragmentColor, in vec2 fragmentPosition)
{
    fragmentColor = vec4(0.0, 0.0, 0.0, 1.0);
    
    const vec3 referencePosition = vec3(0.0, 0.0, 0.0);
   
    float orbitalLongitude = 45.0 + 45.0 * iTime;
    float orbitalLatitude = 45.0;
    float orbitalAltitude = 500.0;
    vec3 orbitalCoordinates = vec3(radians(orbitalLongitude), radians(orbitalLatitude), orbitalAltitude);
    vec3 orbitalDisplacement = vec3(0.0, 0.0, 0.0);
    orbitalDisplacement.x = orbitalCoordinates.z * cos(orbitalCoordinates.y) * cos(orbitalCoordinates.x);
    orbitalDisplacement.y = orbitalCoordinates.z * sin(orbitalCoordinates.y);
    orbitalDisplacement.z = orbitalCoordinates.z * cos(orbitalCoordinates.y) * sin(orbitalCoordinates.x);
    vec3 cameraPosition = referencePosition + orbitalDisplacement;
   
    const vec3 referenceUpward = vec3(0.0, 1.0, 0.0);
    vec3 cameraForward = normalize(referencePosition - cameraPosition);
    vec3 cameraRightward = normalize(cross(referenceUpward, cameraForward));
    vec3 cameraUpward = normalize(cross(cameraForward, cameraRightward));
    mat3 cameraOrientation = mat3(cameraRightward, cameraUpward, cameraForward);
    vec2 projectionPosition = (2.0 * fragmentPosition - iResolution.xy) / min(iResolution.x, iResolution.y);
    float projectionAngle = 60.0;
    float projectionScale = 1.0 / tan(0.5 * radians(projectionAngle));
    vec3 cameraDirection = normalize(cameraOrientation * vec3(projectionPosition, projectionScale));
    
    vec2 radialCoordinates = vec2(atan(cameraDirection.x, cameraDirection.z), asin(cameraDirection.y));
    
    fragmentColor.r = 0.5 + 0.5 * sin(radialCoordinates.x) * sin(radialCoordinates.y);
    fragmentColor.g = 0.5 + 0.5 * cos(radialCoordinates.x) * cos(radialCoordinates.y);
    
    source cameraSource = source(cameraPosition, cameraDirection);
    
    float sampleIntersection = UnfoundIntersection;
    
    if (checkSphereIntersection(cameraSource, sphere(vec3(50.0, -50.0, 50.0), 75.0), sampleIntersection))
    {
        fragmentColor = vec4(1.0, 1.0, 1.0, 1.0);
    }
    
    if (checkSphereIntersection(cameraSource, sphere(vec3(-100.0, 75.0, -25.0),  75.0), sampleIntersection))
    {
        fragmentColor = vec4(1.0, 1.0, 1.0, 1.0);
    }
}
```
