# TERMINOLOGY

Referential transparency
  
  Ensuring something always return the same thing when the same parameters
  are passed. Function should always return this.

  This is problematic with functions like `today()`. Erlang is pragmatic
  about it and in those cases breaks referential transparency.