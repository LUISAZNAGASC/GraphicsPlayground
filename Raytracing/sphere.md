# 0 - VARIABLE DEFINITIONS

| Variable | Definition |
|:---:|:---|
| \(WP\) | World Position |
| \(RO\) | Ray Origin |
| \(RD\) | Ray Direction |
| \(ID\) | Intersection Distance |
| \(SP\) | Sphere Position |
| \(SR\) | Sphere Radius |
| \(RP\) | Relative Position |
| \(QC\) | Quadratic Coefficient |
| \(LC\) | Linear Coefficient |
| \(CC\) | Constant Coefficient |
| \(NC\) | Numeric Constant |

# 1 - RAY DEFINITION

$$
WP = RO + RD \cdot ID
$$

# 2 - SPHERE DEFINITION

$$
(WP - SP) \cdot (WP - SP) = (SR)^2
$$

# 3 - SPHERE SUBSTITUTION

$$
WP = RO + RD \cdot ID
$$

$$
(RO + RD \cdot ID - SP) \cdot (RO + RD \cdot ID - SP) = (SR)^2
$$

# 4 - RELATIVE VECTOR DEFINITION

$$
RP = RO - SP
$$

$$
(RP + RD \cdot ID) \cdot (RP + RD \cdot ID) = (SR)^2
$$

# 5 - EXPANSION OF THE QUADRATIC FORM

$$
(RP \cdot RP) + 2 \cdot (RP \cdot RD) \cdot ID + (RD \cdot RD) \cdot ID^2 = (SR)^2
$$

$$
(RD \cdot RD) \cdot ID^2 + 2 \cdot (RP \cdot RD) \cdot ID + (RP \cdot RP) - (SR)^2 = 0
$$

# 6 - COEFFICIENT DEFINITIONS

$$
QC = (RD \cdot RD)
$$

$$
LC = 2 \cdot (RP \cdot RD)
$$

$$
CC = (RP \cdot RP) - (SR)^2
$$

# 7 - QUADRATIC EQUATION DEFINITION

$$
QC \cdot ID^2 + LC \cdot ID + CC = 0
$$

$$
\frac{QC \cdot ID^2 + LC \cdot ID + CC}{QC} = 0
$$

$$
ID^2 + \frac{LC}{QC} \cdot ID + \frac{CC}{QC} = 0
$$

# 8 - COMPLETING THE SQUARE

$$
(ID + NC)^2 = ID^2 + 2 \cdot NC \cdot ID + NC^2
$$

$$
2 \cdot NC \cdot ID = \frac{LC}{QC} \cdot ID
$$

$$
2 \cdot NC = \frac{LC}{QC}
$$

$$
NC = \frac{LC}{2 \cdot QC}
$$

$$
ID^2 + 2 \cdot NC \cdot ID + \frac{CC}{QC} = 0
$$

$$
ID^2 + 2 \cdot NC \cdot ID = -\frac{CC}{QC}
$$

$$
(ID + NC)^2 = -\frac{CC}{QC} + NC^2
$$

# 9 - FINAL SOLUTION

$$
ID + NC = -\sqrt{-\frac{CC}{QC} + NC^2}
$$

$$
ID + NC = +\sqrt{-\frac{CC}{QC} + NC^2}
$$

$$
ID = -NC - \sqrt{-\frac{CC}{QC} + NC^2}
$$

$$
ID = -NC + \sqrt{-\frac{CC}{QC} + NC^2}
$$

Substituting the value of NC:

$$
ID = -\frac{LC}{2 \cdot QC} - \sqrt{\left(\frac{LC}{2 \cdot QC}\right)^2 - \frac{CC}{QC}}
$$

$$
ID = -\frac{LC}{2 \cdot QC} + \sqrt{\left(\frac{LC}{2 \cdot QC}\right)^2 - \frac{CC}{QC}}
$$

Simplifying:

$$
ID_1 = \frac{-LC - \sqrt{LC^2 - 4 \cdot QC \cdot CC}}{2 \cdot QC}
$$

$$
ID_2 = \frac{-LC + \sqrt{LC^2 - 4 \cdot QC \cdot CC}}{2 \cdot QC}
$$

# SOURCE CODE

```cpp
struct source { vec3 position; vec3 direction; float minimum; float maximum; };

struct sphere { vec3 position; float radius; };

bool trySphereIntersection(const in source sampleSource, const in sphere sampleSphere, out float sample01Intersection)
{
    vec3 relativePosition = sampleSource.position - sampleSphere.position;

    float quadraticCoefficient = dot(sampleSource.direction, sampleSource.direction);
    float linearCoefficient = 2.0 * dot(relativePosition, sampleSource.direction);
    float constantCoefficient = dot(relativePosition, relativePosition) - sampleSphere.radius * sampleSphere.radius;
    float quadraticDiscriminant = linearCoefficient * linearCoefficient - 4.0 * quadraticCoefficient * constantCoefficient;

    if (quadraticDiscriminant < 0.0)
    {
        sample01Intersection = sampleSource.maximum;

        return false;
    }

    float discriminantRadical = sqrt(quadraticDiscriminant);

    float negativeIntersection = (-linearCoefficient - discriminantRadical) / (2.0 * quadraticCoefficient);
    float positiveIntersection = (-linearCoefficient + discriminantRadical) / (2.0 * quadraticCoefficient);
    float incomingIntersection = min(negativeIntersection, positiveIntersection);
    float outgoingIntersection = max(negativeIntersection, positiveIntersection);

    if (incomingIntersection >= sampleSource.minimum && incomingIntersection <= sampleSource.maximum)
    {
         sample01Intersection = incomingIntersection;

         return true;
    }

    if (outgoingIntersection >= sampleSource.minimum && outgoingIntersection <= sampleSource.maximum)
    {
        sample01Intersection = outgoingIntersection;

        return true;
    }

    return false;
}
```
