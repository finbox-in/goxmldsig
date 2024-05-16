# I've spent days trying to wrap my mind over UIDAI's broken documentation. Please don't waste yours. Use this snippet to validate Aadhaar Mobile and Email. Hail UIDAI 🙏

```go
type PersonalInfoValidation struct {
	MobileMatch bool `json:"mobile_match"`
	EmailMatch  bool `json:"email_match"`
}

func validatePersonalInfo(el *etree.Element, actualMobile, actualEmail, shareCode string, aadhaarLastDigit int) PersonalInfoValidation {
	mobile := el.FindElement("//Poi").SelectAttrValue("m", "default-mobile")
	email := el.FindElement("//Poi").SelectAttrValue("e", "default-email")

	if aadhaarLastDigit == 0 {
		aadhaarLastDigit = 1
	}
	var response PersonalInfoValidation

	if mobile != "default-mobile" {
		lastInput := actualMobile + shareCode
		for i := 0; i < aadhaarLastDigit; i++ {
			h := sha256.Sum256([]byte(lastInput))
			lastInput = hex.EncodeToString(h[:])
		}
		response.MobileMatch = lastInput == mobile
	}

	if email != "default-email" {
		lastInput := actualEmail + shareCode
		for i := 0; i < aadhaarLastDigit; i++ {
			h := sha256.Sum256([]byte(lastInput))
			lastInput = hex.EncodeToString(h[:])
		}
		response.EmailMatch = lastInput == email
	}

	return response
}
```
