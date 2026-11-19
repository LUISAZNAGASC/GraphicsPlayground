```glsl
// ==================================================================================================
// 0.0 - DEFINITION OF THE VARIABLES USED IN THE DERIVATION
// ==================================================================================================
//
// WP = World Position
// RO = Ray Origin
// RD = Ray Direction
// ID = Intersection Distance
// SP = Sphere Position
// SR = Sphere Radius
// RP = Relative Position
// QC = Quadratic Coefficient
// LC = Linear Coefficient
// CC = Constant Coefficient
// AC = Auxiliary Constant
//
// ==================================================================================================
// 1.0 - MATHEMATICAL DEFINITION OF THE RAY EQUATION
// ==================================================================================================
//
// Define the ray using its origin and direction:
//
// WP = RO + RD * ID;
//
// ==================================================================================================
// 2.0 - MATHEMATICAL DEFINITION OF THE SPHERE EQUATION
// ==================================================================================================
//
// Define the sphere using its center and radius:
//
// (WP - SP) · (WP - SP) = (SR)^2.0;
//
// ==================================================================================================
// 3.0 - SUBSTITUTION OF THE RAY EQUATION INTO THE SPHERE EQUATION
// ==================================================================================================
//
// Substitute the ray equation into the sphere equation:
//
// ((RO + RD * ID) - SP) · ((RO + RD * ID) - SP) = (SR)^2.0;
//
// ==================================================================================================
// 4.0 - DEFINITION OF THE RELATIVE POSITION VECTOR
// ==================================================================================================
//
// Define the vector from the sphere center to the ray origin:
//
// RP = RO - SP;
//
// Substitute the relative position vector into the sphere equation:
//
// (RP + RD * ID) · (RP + RD * ID) = (SR)^2.0;
//
// ==================================================================================================
// 5.0 - EXPANSION AND REARRANGEMENT OF THE QUADRATIC FORM
// ==================================================================================================
//
// Expand the dot product into individual dot products:
//
// (RP · RP) + 2.0 * (RP · RD) * ID + (RD · RD) * (ID)^2.0 = (SR)^2.0;
//
// Rearrange the expanded expression into standard quadratic form:
//
// (RD · RD) * (ID)^2.0 + 2.0 * (RP · RD) * ID + (RP · RP) - (SR)^2.0 = 0.0;
//
// ==================================================================================================
// 6.0 - IDENTIFICATION OF THE QUADRATIC EQUATION COEFFICIENTS
// ==================================================================================================
//
// Identify the quadratic, linear, and constant coefficients:
//
// QC = (RD · RD);
// LC = 2.0 * (RP · RD);
// CC = (RP · RP) - (SR)^2.0;
//
// ==================================================================================================
// 7.0 - FORMULATION OF THE STANDARD QUADRATIC EQUATION
// ==================================================================================================
//
// Express the intersection equation in standard quadratic form:
//
// QC * (ID)^2.0 + LC * ID + CC = 0.0;
//
// Normalize the quadratic equation by dividing by QC:
//
// (ID)^2.0 + (LC / QC) * ID + CC / QC = 0.0;
//
// ==================================================================================================
// 8.0 - TRANSFORMATION OF THE QUADRATIC EQUATION BY COMPLETING THE SQUARE
// ==================================================================================================
//
// Expand the binomial square into its individual terms:
//
// (ID + AC)^2.0 = (ID)^2.0 + 2.0 * AC * ID + (AC)^2.0;
//
// Match the linear coefficients to determine the auxiliary constant:
//
// 2.0 * AC = LC / QC => AC = LC / (2.0 * QC);
//
// Substitute the auxiliary constant into the normalized quadratic equation:
//
// (ID)^2.0 + 2.0 * AC * ID + CC / QC = 0.0;
//
// Rearrange the equation to isolate the squared terms:
//
// (ID)^2.0 + 2.0 * AC * ID = -CC / QC;
//
// Add the missing squared term to complete the square:
//
// (ID + AC)^2.0 = -CC / QC + (AC)^2.0;
//
// ==================================================================================================
// 9.0 - DERIVATION OF THE RAY-SPHERE INTERSECTION SOLUTIONS
// ==================================================================================================
//
// Take the square root of both sides to obtain both solutions:
//
// ID + AC = ±(-CC / QC + (AC)^2.0)^0.5;
//
// Isolate the intersection parameter:
//
// ID = -AC ± (-CC / QC + (AC)^2.0)^0.5;
//
// Substitute the auxiliary constant:
//
// ID = -(LC / (2.0 * QC)) ± (-CC / QC + (LC / (2.0 * QC))^2.0)^0.5;
//
// Simplify into the standard quadratic formula:
//
// ID = (-LC ± (LC^2.0 - 4.0 * QC * CC)^0.5) / (2.0 * QC);
//
// ==================================================================================================
// IMPLEMENTATION OF THE RAY-SPHERE INTERSECTION
// ==================================================================================================

struct source { vec3 position; vec3 direction; float minimum; float maximum; };

struct sphere { vec3 position; float radius; };

const float SENTINEL_INTERSECTION = 3.402823466e+38;

// Case 1: Check whether either intersection lies within the source interval.
bool trySphereIntersection(const in source sampleSource, const in sphere sampleSphere)
{
    vec3 relativePosition = sampleSource.position - sampleSphere.position;
    
    float quadraticCoefficient = dot(sampleSource.direction, sampleSource.direction);
    float linearCoefficient = 2.0 * dot(relativePosition, sampleSource.direction);
    float constantCoefficient = dot(relativePosition, relativePosition) - sampleSphere.radius * sampleSphere.radius;
    float quadraticDiscriminant = linearCoefficient * linearCoefficient - 4.0 * quadraticCoefficient * constantCoefficient;
    
    if (quadraticDiscriminant < 0.0)
    {
        return false;
    }
    
    float discriminantRadical = sqrt(quadraticDiscriminant);
    
    float negativeIntersection = (-linearCoefficient - discriminantRadical) / (2.0 * quadraticCoefficient);
    float positiveIntersection = (-linearCoefficient + discriminantRadical) / (2.0 * quadraticCoefficient);
    float incomingIntersection = min(negativeIntersection, positiveIntersection);
    float outgoingIntersection = max(negativeIntersection, positiveIntersection);
    
    if (incomingIntersection >= sampleSource.minimum && incomingIntersection <= sampleSource.maximum)
    {
        return true;
    }
    
    if (outgoingIntersection >= sampleSource.minimum && outgoingIntersection <= sampleSource.maximum)
    {
        return true;
    }
    
    return false;
}

// Case 2: Return the nearest intersection within the source interval, or SENTINEL_INTERSECTION if neither intersection is valid.
void trySphereIntersection(const in source sampleSource, const in sphere sampleSphere, out float sample01Intersection)
{
    vec3 relativePosition = sampleSource.position - sampleSphere.position;
    
    float quadraticCoefficient = dot(sampleSource.direction, sampleSource.direction);
    float linearCoefficient = 2.0 * dot(relativePosition, sampleSource.direction);
    float constantCoefficient = dot(relativePosition, relativePosition) - sampleSphere.radius * sampleSphere.radius;
    float quadraticDiscriminant = linearCoefficient * linearCoefficient - 4.0 * quadraticCoefficient * constantCoefficient;
    
    if (quadraticDiscriminant < 0.0)
    {
        sample01Intersection = SENTINEL_INTERSECTION;
        
        return;
    }
    
    float discriminantRadical = sqrt(quadraticDiscriminant);
    
    float negativeIntersection = (-linearCoefficient - discriminantRadical) / (2.0 * quadraticCoefficient);
    float positiveIntersection = (-linearCoefficient + discriminantRadical) / (2.0 * quadraticCoefficient);
    float incomingIntersection = min(negativeIntersection, positiveIntersection);
    float outgoingIntersection = max(negativeIntersection, positiveIntersection);
    
    if (incomingIntersection >= sampleSource.minimum && incomingIntersection <= sampleSource.maximum)
    {
        sample01Intersection = incomingIntersection;
        
        return;
    }
    
    if (outgoingIntersection >= sampleSource.minimum && outgoingIntersection <= sampleSource.maximum)
    {
        sample01Intersection = outgoingIntersection;
        
        return;
    }

    sample01Intersection = SENTINEL_INTERSECTION;
}

// Case 3: Return both intersections independently when they lie within the source interval; otherwise, return SENTINEL_INTERSECTION for that root.
void trySphereIntersection(const in source sampleSource, const in sphere sampleSphere, out float sample01Intersection, out float sample02Intersection)
{
    vec3 relativePosition = sampleSource.position - sampleSphere.position;

    float quadraticCoefficient = dot(sampleSource.direction, sampleSource.direction);
    float linearCoefficient = 2.0 * dot(relativePosition, sampleSource.direction);
    float constantCoefficient = dot(relativePosition, relativePosition) - sampleSphere.radius * sampleSphere.radius;
    float quadraticDiscriminant = linearCoefficient * linearCoefficient - 4.0 * quadraticCoefficient * constantCoefficient;
    
    if (quadraticDiscriminant < 0.0)
    {
        sample01Intersection = SENTINEL_INTERSECTION;
        sample02Intersection = SENTINEL_INTERSECTION;
        
        return;
    }
    
    float discriminantRadical = sqrt(quadraticDiscriminant);
    
    float negativeIntersection = (-linearCoefficient - discriminantRadical) / (2.0 * quadraticCoefficient);
    float positiveIntersection = (-linearCoefficient + discriminantRadical) / (2.0 * quadraticCoefficient);
    float incomingIntersection = min(negativeIntersection, positiveIntersection);
    float outgoingIntersection = max(negativeIntersection, positiveIntersection);
    
    if (incomingIntersection >= sampleSource.minimum && incomingIntersection <= sampleSource.maximum)
    {
        sample01Intersection = incomingIntersection;
    }
    else
    {
        sample01Intersection = SENTINEL_INTERSECTION;
    }
    
    if (outgoingIntersection >= sampleSource.minimum && outgoingIntersection <= sampleSource.maximum)
    {
        sample02Intersection = outgoingIntersection;
    }
    else
    {
        sample02Intersection = SENTINEL_INTERSECTION;
    }
}
```
