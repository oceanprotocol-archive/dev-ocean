**Table of Contents**

[TOC]

# Integration Testing and Network Reliability

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [BCP 14](https://tools.ietf.org/html/bcp14) \[[RFC2119](https://tools.ietf.org/html/rfc2119)\] \[[RFC8174](https://tools.ietf.org/html/rfc8174)\] when, and only when, they appear in all capitals, as shown here.

## Motivation

The main motivations are to:

* Define and enumerate the different languages and environments for which testing and monitoring are required
* Specify the requirements for testing according to each combination of language and environment
* Identify and select promising testing frameworks 

## Context

TODO

## Test scenarios (examples)

### Testing against Spree network

```
WHERE configuration is a key-value of versions and other parameters

FOR configuration IN configurations_list: 
    Install python packages
    Set env variables
    Pull docker images
    Spin up containers 
    Execute integration tests
    Return status, report
END LOOP
```

### Testing against Nile/POA network

```
WHERE configuration is a key-value of versions and other parameters

FOR configuration IN configurations_list: 
    Install python packages
    Set env variables
    Execute integration tests
    Return status, report
END LOOP



```

### Data Commons management, seeding, and validation

```
WHERE dataset is a collection of files and metadata conforming to OEP stored locally or at some URL

FOR dataset IN data_definition_list:
    Check file sizes and access
    Check metadata valid
    Check target destination (S3 bucket, Azure, etc.)
    Check if registered in Aquarius and resolvable
    Check if consumable/purchasable through Brizo
    IF NOT VALID: (not uploaded, not conformant) 
        Initiate transfer, update Aquarius, report error, etc.
    END IF
END LOOP
```

### Simulation and Load Testing

```
WHERE simulation_profile is a configuration for a simulation including things like;
    - Number users
    - Behaviour of users
    - Number of assets
    - Type of access, etc

LOAD intitial environment (Assets, accounts, simulated currency, behaviours)
LOAD components (Spree/Nile, Aquarius, etc.) (can be sub-DAG from tests)
WHILE simulation
    FOR user IN users
        EXECUTE action from behaviour list
    END LOOP
    Reporting and checks
END LOOP
```

## References

TODO